---
site:
  outline_maxdepth: 2
---

# Inference and predictions

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Running inference and inspecting raw Mask R-CNN predictions
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

You trained a model on the previous page. This page applies it to new imagery and works through exactly what `geoai` hands back, before the next page cleans and vectorizes that output into something GIS-ready.

Instance segmentation inference returns more than a single raster. Knowing what each piece of the output actually represents, rather than treating the whole result as one opaque object, is what lets you debug a poor prediction and decide which parts of the output your project actually needs.

---

## 2. Core idea

`geoai.instance_segmentation()` runs sliding-window inference the same way detection and semantic segmentation did in earlier lessons, and returns a dictionary with four related but distinct outputs: an instance raster, a class-label raster, a confidence-score raster, and, optionally, a vectorized set of polygons.

---

## 3. Workflow

Once model training produces a saved checkpoint (`best_model.pth`), the next step is {term}`Inference`—applying the trained network to unseen test imagery. Instance segmentation produces multi-layered outputs containing spatial geometry, class identities, unique instance IDs, and prediction confidence. Inspecting raw model predictions before applying post-processing or cleaning filters is essential for understanding true model behavior and diagnosing failure modes.

---

### A. Test image selection

We re-establish the dataset environment and select an independent test image from the held-out test split:

```{code-cell} python
from pathlib import Path
import geoai

# Download and prepare dataset pathways
geoai.download_ftw(countries=["luxembourg"], output_dir="ftw_data")
data = geoai.prepare_ftw("ftw_data", country="luxembourg")

# Select test image deterministically
test_images = sorted(Path(data["test_dir"]).glob("*.tif"))
test_image_path = str(test_images[0])

print(f"Selected test tile: {test_image_path}")

```

Using `sorted()` ensures file order remains deterministic and reproducible across execution environments.

---

### B. Executing instance segmentation

Satellite scenes are frequently larger than the fixed $256 \times 256$ pixel input size used during training. We apply the model using {term}`Tiled Inference`, sliding a window across the target image and stitching predictions together:

```{code-cell} python
model_path = "field_boundaries/models/best_model.pth"
masks_path = "field_boundary_prediction.tif"

result = geoai.instance_segmentation(
    input_path=test_image_path,
    output_path=masks_path,
    model_path=model_path,
    num_classes=2,
    num_channels=4,
    window_size=256,
    overlap=128,
    confidence_threshold=0.5,
    batch_size=4,
    vectorize=True,
    class_names=["background", "field"],
)

```

Tiled inference parameters dictate both prediction quality and computational efficiency:

* **`window_size=256`:** Matches the training chip dimensions. Maintaining equal window sizes between training and inference preserves object scale relative to the model's learned receptive field.
* **`overlap=128` (50% Overlap):** Slides the window in 128-pixel increments, ensuring every pixel is evaluated in multiple overlapping passes. This prevents target features located near tile margins from being truncated or missed entirely.
* **`confidence_threshold=0.5`:** Discards candidate detections with certainty scores below $50\%$. Lowering this threshold increases recall (fewer missed fields, but more false positives), while raising it increases precision (fewer false positives, but more missed fields).
* **`batch_size=4`:** Controls the number of inference windows processed concurrently on the GPU.

---

### C. Deconstructing the result dictionary

The `instance_segmentation()` function returns a structured dictionary containing four distinct analytical representations:

| Output Key | Data Structure | Description & Analytical Purpose |
| --- | --- | --- |
| `"instance"` | Raster (`uint16`) | A label matrix where background pixels equal `0` and every detected field is assigned a unique positive integer ID ($1, 2, 3 \dots$). |
| `"class_label"` | Raster (`uint8`) | A semantic mask storing binary classification results ($0$ = background, $1$ = field). |
| `"score"` | Raster (`float32`) | A continuous spatial surface of per-pixel confidence values ($0.0 \text{ to } 1.0$). |
| `"vector"` | GeoDataFrame | Vector polygons generated when `vectorize=True`, containing geometries attributed with `instance_id`, `class_name`, and `score`. |

---

### D. Visualizing raw raster predictions

Displaying raw raster outputs provides immediate visual feedback on model performance before any post-processing smoothing or polygon filtering occurs:

```{code-cell} python
# Display unique instance IDs using a categorical colormap
geoai.view_raster(
    result["instance"], 
    nodata=0, 
    cmap="tab20", 
    basemap=test_image_path, 
    backend="ipyleaflet"
)

# Display binary semantic class predictions
geoai.view_raster(
    result["class_label"], 
    nodata=0, 
    cmap="binary", 
    basemap=test_image_path, 
    backend="ipyleaflet"
)

# Display pixel confidence scores
geoai.view_raster(
    result["score"], 
    nodata=0, 
    basemap=test_image_path, 
    backend="ipyleaflet"
)

```

When inspecting raw rasters, evaluate predictions against these active diagnostic questions:

* **Instance Separation:** Do touching, adjacent agricultural parcels receive distinct colors (separate IDs), or do they merge into a single uniform patch?
* **Edge Continuity:** Are field boundaries continuous, or do noticeable line seams appear along window tiling margins?
* **Confidence vs. Geometry:** Does a high score in the confidence raster correlate with clean geometric boundaries, or does the model exhibit high confidence on distorted shapes?

---

### E. Inspecting raw vectorized outputs

When `vectorize=True`, the pipeline polygonizes raster instance masks into a GeoPandas `GeoDataFrame`. Interactive vector visualization allows styling polygons by model confidence:

```{code-cell} python
geoai.view_vector_interactive(
    result["vector"], 
    tiles=test_image_path, 
    column="score"
)

```

Styling vector outputs by `score` immediately highlights low-confidence candidate polygons (e.g., scores between $0.50$ and $0.65$). These low-certainty features represent prime candidates for targeted manual QA/QC or post-processing threshold adjustments.

---

### F. Diagnosing failure modes

Instance segmentation models exhibit structural and spatial errors beyond standard misclassification. Evaluating raw test outputs requires identifying four common error categories:

```text
1. Missed Instance (False Negative)        2. Spurious Instance (False Positive)
Reference:  [ Field A ] [ Field B ]        Reference:  [ Field A ] [  Background  ]
Prediction: [ Field A ] [    ---   ]        Prediction: [ Field A ] [ Ghost Parcel ]

3. Merged Instances (Structural Error)    4. Split Instance (Structural Error)
Reference:  [ Field A ] [ Field B ]        Reference:  [     Field A     ]
Prediction: [   Combined Polygon  ]        Prediction: [ Part 1 ][ Part 2 ]

```

* **False Positives (Spurious Instances):** Non-agricultural features (e.g., clearings, sports fields, road loops) incorrectly identified as fields.
* *Diagnostic response:* Incorporate broader background training samples or increase `confidence_threshold`.

* **False Negatives (Missed Instances):** Real fields omitted due to small spatial size, unusual crop reflectance, or low contrast with surroundings.
* *Diagnostic response:* Lower `confidence_threshold` or add multi-temporal imagery (`window_b`) to enhance spectral contrast.

* **Merged Instances:** Adjacent parcels represented as a single contiguous polygon.
* *Impact:* Artificially depresses total field counts and distorts mean field area metrics.

* **Split Instances:** A single continuous agricultural field fragmented into multiple separate polygons.
* *Impact:* Artificially inflates total field counts and creates spuriously small polygons.

---

### G. Quantitative instance evaluation

While visual inspection isolates specific spatial failures, rigorous evaluation requires quantitative performance metrics. Instance segmentation metrics evaluate both bounding box detection and mask geometry overlap:

* **Intersection over Union (IoU):** Measures spatial overlap between predicted mask $A$ and ground-truth mask $B$:

$$\text{IoU}(A, B) = \frac{\vert{}A \cap B\vert{}}{\vert{}A \cup B\vert{}}$$

A predicted instance is typically counted as a {term}`True Positive` if its $\text{IoU} \ge 0.50$.

* **Average Precision (AP):** Summarizes detection and segmentation accuracy across varying confidence thresholds. $\text{mAP}_{50}$ evaluates performance at $50\%$ IoU overlap, while $\text{mAP}_{50:95}$ averages precision across strict overlap thresholds ($0.50 \text{ to } 0.95$).

```{important}
Align your evaluation metric with your project's primary analytical goal. Workflows designed for **object counting** must prioritize minimizing split/merged instance errors, whereas workflows designed for **area and boundary extraction** must prioritize high mask IoU and geometric boundary accuracy.

```

---

## 4. Python reactivation

`result["instance"]`, `result["class_label"]`, `result["score"]`, and `result["vector"]` are all dictionary lookups by key, the same pattern used for `data["images_dir"]` on the previous page, just applied here to a function's return value instead of a prepared dataset.

---

## 5. Common pitfalls

* **Confusing the four result keys.** `"instance"` and `"class_label"` look superficially similar as rasters but answer different questions, exactly the class-versus-instance distinction from earlier in this lesson; know which one you are looking at.
* **Mismatching `window_size` with the training chip size without a specific reason.** Consistency here reduces one source of unexpected behavior.
* **Setting `overlap` too small.** Objects near tile boundaries are more likely to be split or missed with too little overlap, the same tiling lesson from L06 and L07.
* **Forgetting `vectorize=True` when the project needs the polygon output.** Without it, `result` will not include a `"vector"` entry, and you would need to vectorize the raster separately.

---

## 6. Mini task

You inspect `result["instance"]` and see 12 distinct non-zero values, but `result["vector"]` contains only 10 rows. What is the most likely explanation, and how would you check it?

:::{dropdown} Sample solution
:class: note

The most likely explanation is that the vectorization step (or a filtering step applied to it) dropped two very small or otherwise invalid detections, for example instances too small to form a valid polygon, rather than an error in the count. You would check this by comparing the actual instance ID values present in the raster (via `numpy.unique()`) against the `instance` IDs listed in the `result["vector"]` GeoDataFrame, and inspect the two missing IDs directly in the raster to see whether they correspond to tiny, likely spurious detections.
:::

---

## 7. Key takeaways

* `geoai.instance_segmentation()` returns a dictionary with instance, class-label, score, and (optionally) vector outputs, each answering a different question about the same prediction.
* `window_size` and `overlap` carry the same tiling considerations introduced for detection and semantic segmentation in earlier lessons.
* Coloring vectorized predictions by confidence score is a quick way to prioritize which detections to check most carefully.
* The next page cleans this raw output and turns it into a finished, GIS-ready deliverable.

### Further reading

* OpenGeoAI, ["Instance Segmentation"](https://opengeoai.org/examples/instance_segmentation/) — a related worked example of running and interpreting instance segmentation inference with `geoai`.
