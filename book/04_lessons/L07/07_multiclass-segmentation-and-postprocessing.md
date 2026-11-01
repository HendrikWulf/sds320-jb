---
site:
  outline_maxdepth: 1
---

# Multiclass segmentation

<!-- markdownlint-disable MD033 -->
<div class="page-subtitle">
From four-band aerial imagery to a 13-class land-cover map
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

In earlier chapters, you trained or applied models that distinguished a target class, such as water, from everything else. Real landscapes rarely fit into just two categories. A land-cover map might distinguish water, vegetation, tree canopy, buildings, roads, and wetlands at every pixel.

In this chapter, you will train a {term}`semantic segmentation <Semantic Segmentation>` model to predict land cover from four-band aerial imagery. Both the training and test areas have reference land-cover maps. You can therefore measure which classes the model handles well, which it confuses, and whether its output is useful for your own spatial analysis.

Our applied question is:

> How well can a U-Net trained on one NAIP aerial-image area predict 13 Chesapeake land-cover classes in a neighbouring, geographically separate area?

You will follow this workflow:

**Download → inspect → tile → train → infer → compare with reference → evaluate per class → extract polygons.**

---

## 2. Core idea

A multi-class segmentation model assigns one of several integer labels to each pixel. We use the [Chesapeake 13-class](https://www.chesapeakeconservancy.org/projects/cbp-land-use-land-cover-data-project) scheme from the Chesapeake Bay watershed, paired with four-band National Agriculture Imagery Program ({abbr}`NAIP (National Agriculture Imagery Program)`) data.

The numerical scheme includes values **0–12**, but there is an important distinction: **0 means NoData/unlabelled**, while **1–12 describe land-cover categories**. We retain `num_classes=13` to match the raster encoding, but exclude reference pixels with value 0 from our evaluation and ignore them when calculating the training loss.

| ID | Chesapeake class | ID | Chesapeake class |
|---:|---|---:|---|
| 0 | NoData / unlabelled | 7 | Impervious structures |
| 1 | Water | 8 | Other impervious surfaces |
| 2 | Emergent wetlands | 9 | Impervious roads |
| 3 | Tree canopy | 10 | Tree canopy over structures |
| 4 | Scrub / shrub | 11 | Tree canopy over other impervious surfaces |
| 5 | Low vegetation | 12 | Tree canopy over roads |
| 6 | Barren | | |

The four image bands are **red, green, blue, and near-infrared (NIR)**, in that order. The model receives all four channels, but the RGB previews use only bands `[1, 2, 3]`.

```{admonition} Two different kinds of evaluation
:class: important
**Validation during training** helps you decide whether a model is learning. With a random split of tiles from one image, nearby training and validation tiles can remain spatially related—even if the tiles do not overlap. **Independent testing** uses the geographically separate `naip_lc_test_*` pair. Keep the test labels out of model development; use them to assess the final model, not to tune it repeatedly.
```

| Split | Aerial image | Reference label |
|---|---|---|
| Training | `naip_lc_train_image.tif` | `naip_lc_train_landcover.tif` |
| Independent test | `naip_lc_test_image.tif` | `naip_lc_test_landcover.tif` |

Both pairs were designed to have the same image dimensions, approximately 2:1 aspect ratio, and 1 m pixels. Training and test **have different locations**; each image must align exactly with its *own* labels.

---

## 3. Download the datasets

### A. Set up the working environment

Use the GeoAI environment from the previous L07 chapters. If you need to install packages in a new runtime, consult the [GeoAI installation instructions](https://opengeoai.org/installation/) first. The code below uses GeoAI, Rasterio, NumPy, Pandas, and Matplotlib. The `sklearn` package is **not** required for the independent evaluation below.

```{code-cell} python
from pathlib import Path

import geoai
import numpy as np
import pandas as pd
import rasterio
from rasterio.enums import Resampling
from matplotlib import pyplot as plt
from matplotlib.colors import ListedColormap, BoundaryNorm
from matplotlib.patches import Patch

WORK_DIR = Path("sds320_landcover")
DATA_DIR = WORK_DIR / "data"
TILES_DIR = WORK_DIR / "tiles"
MODEL_DIR = WORK_DIR / "unet_models"
OUTPUT_DIR = WORK_DIR / "outputs"

for folder in (DATA_DIR, TILES_DIR, MODEL_DIR, OUTPUT_DIR):
    folder.mkdir(parents=True, exist_ok=True)

BASE_URL = "https://data.source.coop/giuz/sds320/L07/data/"
```

### B. Download the four GeoTIFFs

We download each file only if it is not already available locally. The training labels will be used for creating tiles; the test labels stay separate until the final evaluation.

```{code-cell} python
filenames = {
    "train_image": "naip_lc_train_image.tif",
    "train_labels": "naip_lc_train_landcover.tif",
    "test_image": "naip_lc_test_image.tif",
    "test_labels": "naip_lc_test_landcover.tif",
}

paths = {}
for key, filename in filenames.items():
    local_path = DATA_DIR / filename
    if not local_path.exists():
        geoai.download_file(BASE_URL + filename, output_path=str(local_path))
    if not local_path.is_file():
        raise FileNotFoundError(f"Dataset not available: {local_path}")
    paths[key] = local_path
    print(f"{key:13s}: {local_path}")

train_raster_path = paths["train_image"]
train_landcover_path = paths["train_labels"]
test_raster_path = paths["test_image"]
test_landcover_path = paths["test_labels"]
```

### C. Verify raster alignment

Before training, check the dimensions, {term}`coordinate reference system <Coordinate Reference System>` ({abbr}`CRS (Coordinate Reference System)`), pixel grid, and class values. If an image and its labels are offset by even one pixel, the model will be taught the wrong boundaries.

```{code-cell} python
def inspect_pair(image_path, labels_path, split):
    with rasterio.open(image_path) as image, rasterio.open(labels_path) as labels:
        assert image.count == 4, f"{split}: expected four NAIP channels"
        assert labels.count == 1, f"{split}: expected one label band"
        assert image.width == labels.width and image.height == labels.height
        assert image.crs == labels.crs, f"{split}: CRS mismatch"
        assert image.transform == labels.transform, f"{split}: grid mismatch"
        assert image.crs is not None, f"{split}: missing CRS"
        assert np.issubdtype(np.dtype(labels.dtypes[0]), np.integer)

        values, counts = np.unique(labels.read(1), return_counts=True)
        assert set(values).issubset(set(range(13))), (
            f"{split}: unexpected class IDs {values}"
        )
        valid_fraction = counts[values > 0].sum() / counts.sum()
        print(f"{split.upper()}: {image.width:,} × {image.height:,} pixels")
        print(f"  CRS: {image.crs} | resolution: {image.res}")
        print(f"  bands: {image.count} | stored descriptions: {image.descriptions}")
        print(f"  labels: {values.tolist()} | labelled pixels: {valid_fraction:.1%}")
        return (image.height, image.width)

train_shape = inspect_pair(train_raster_path, train_landcover_path, "train")
test_shape = inspect_pair(test_raster_path, test_landcover_path, "test")
assert train_shape == test_shape, "Training and test image sizes differ."
```

The data-preparation workflow also intended the areas to be spatially separated. Check their footprints rather than assuming separate filenames guarantee that.

```{code-cell} python
from shapely.geometry import box
from rasterio.warp import transform_bounds

with rasterio.open(train_raster_path) as tr, rasterio.open(test_raster_path) as te:
    test_bounds_in_train_crs = transform_bounds(
        te.crs, tr.crs, *te.bounds, densify_pts=21
    )
    separated = box(*tr.bounds).disjoint(box(*test_bounds_in_train_crs))
    print("Training/test footprints are disjoint:", separated)
    if not separated:
        print("WARNING: the two areas overlap; review the independent-test design.")
```

### D. Define a consistent legend

Use the **same** colour scheme for training labels, test labels, and predicted classes. This makes disagreement much easier to spot. The palette follows the one provided in the Chesapeake data-preparation script.

```{code-cell} python
CLASS_NAMES = {
    0: "NoData", 1: "Water", 2: "Emergent wetlands",
    3: "Tree canopy", 4: "Scrub / shrub", 5: "Low vegetation",
    6: "Barren", 7: "Structures", 8: "Other impervious",
    9: "Roads", 10: "Trees over structures",
    11: "Trees over impervious", 12: "Trees over roads",
}

CLASS_COLORS = {
    0: "#dddddd", 1: "#00c5ff", 2: "#00a884",
    3: "#267300", 4: "#4ce600", 5: "#a3ff73",
    6: "#ffaa00", 7: "#ff0000", 8: "#9c9c9c",
    9: "#222222", 10: "#737300", 11: "#e6e600",
    12: "#ffff73",
}

LC_CMAP = ListedColormap([CLASS_COLORS[i] for i in range(13)])
LC_NORM = BoundaryNorm(np.arange(-0.5, 13.5, 1), LC_CMAP.N)
LEGEND = [
    Patch(facecolor=CLASS_COLORS[i], edgecolor="gray",
          label=f"{i}: {CLASS_NAMES[i]}")
    for i in range(1, 13)
]
```

### E. Look at both image–label pairs

First, compare the aerial image with its reference categories. For these large rasters, the preview is resampled in memory, **without changing the files used for training**. Categorical labels must be previewed with *nearest-neighbour* resampling so that class values are not blended.

```{code-cell} python
def raster_preview(path, indexes, max_width=1100, categorical=False):
    with rasterio.open(path) as src:
        width = min(src.width, max_width)
        height = max(1, round(src.height * width / src.width))
        method = Resampling.nearest if categorical else Resampling.average
        data = src.read(
            indexes,
            out_shape=(len(indexes), height, width),
            resampling=method,
        )
    if categorical:
        return data[0]
    rgb = np.moveaxis(data[:3], 0, -1).astype("float32")
    # Display stretch only; no change to training input.
    p2 = np.percentile(rgb, 2, axis=(0, 1))
    p98 = np.percentile(rgb, 98, axis=(0, 1))
    return np.clip((rgb - p2) / np.maximum(p98 - p2, 1), 0, 1)

fig, axes = plt.subplots(2, 2, figsize=(16, 9))
for row, (name, image, labels) in enumerate([
    ("Training area", train_raster_path, train_landcover_path),
    ("Independent test area", test_raster_path, test_landcover_path),
]):
    axes[row, 0].imshow(raster_preview(image, [1, 2, 3]))
    axes[row, 0].set_title(f"{name}: NAIP RGB")
    classes = raster_preview(labels, [1], categorical=True)
    axes[row, 1].imshow(
        np.ma.masked_where(classes == 0, classes),
        cmap=LC_CMAP, norm=LC_NORM, interpolation="nearest"
    )
    axes[row, 1].set_title(f"{name}: Chesapeake reference")
    for ax in axes[row]:
        ax.set_axis_off()
fig.subplots_adjust(bottom=0.16, hspace=0.16)
fig.legend(handles=LEGEND, loc="lower center", ncol=4, fontsize=9,
           bbox_to_anchor=(0.5, 0.01))
plt.show()
```

**Inspect the result:** Do roads, forest boundaries, water, and buildings appear consistently labelled? Look for plausible temporal mismatches—for example, a new structure visible in the imagery but absent from the reference. Those errors can arise even if the image and label pixels are geometrically aligned.

### F. Compare the class balance

A multi-class model can be dominated by common categories. Here you can check whether the **same classes occur in both areas** and how their relative abundance changes. Exclude class 0 because it does not describe land cover.

```{code-cell} python
def class_histogram(path):
    with rasterio.open(path) as src:
        counts = np.bincount(src.read(1).ravel(), minlength=13)[:13]
    return counts

train_counts = class_histogram(train_landcover_path)
test_counts = class_histogram(test_landcover_path)

shares = pd.DataFrame({
    "Class": [CLASS_NAMES[i] for i in range(1, 13)],
    "Training (%)": 100 * train_counts[1:] / max(1, train_counts[1:].sum()),
    "Testing (%)": 100 * test_counts[1:] / max(1, test_counts[1:].sum()),
}, index=np.arange(1, 13))
display(shares.round(2))

ax = shares.plot.bar(figsize=(13, 4), width=0.8)
ax.set_xlabel("Chesapeake class ID")
ax.set_ylabel("Share of labelled pixels (%)")
ax.set_xticklabels([f"{i}" for i in range(1, 13)], rotation=0)
ax.legend(loc="upper right")
plt.tight_layout()
plt.show()
```

```{admonition} Class presence is not class balance
:class: note
An uncommon class may be present in a test area but nearly absent from training. This does not invalidate the demonstration, but it can explain poor performance for that class. The original test-area selection considered class distributions to make the exercise more useful; that convenience should not be mistaken for a randomly sampled regional benchmark.
```

---

## 4. Prepare training tiles

### A. Create matched patches

U-Net processes fixed-size image patches. The usual GeoAI function, `geoai.export_geotiff_tiles()`, slices the NAIP image and corresponding label raster into **matching GeoTIFF tiles**.

We start with `tile_size=512` and `stride=512`: neighbouring tiles do **not** overlap. This creates fewer samples than a 256-pixel stride, but reduces redundant pixels and makes the small teaching experiment more manageable. A random validation split can still share similar nearby landscapes, even without pixel overlap.

```{code-cell} python
TILE_SIZE = 512
STRIDE = 512

geoai.export_geotiff_tiles(
    in_raster=str(train_raster_path),
    in_class_data=str(train_landcover_path),
    out_folder=str(TILES_DIR),
    tile_size=TILE_SIZE,
    stride=STRIDE,
    buffer_radius=0,
    skip_empty_tiles=False,
)

image_tiles = sorted((TILES_DIR / "images").glob("*.tif"))
label_tiles = sorted((TILES_DIR / "labels").glob("*.tif"))
print(f"Image tiles: {len(image_tiles)} | Label tiles: {len(label_tiles)}")
assert image_tiles and len(image_tiles) == len(label_tiles)
```

### B. Inspect tiles and labels

The tile folders hold image and label patches with corresponding spatial extents. The following plot checks one pair; it also serves as a reminder of the Rasterio `.read()` indexing convention.

```{code-cell} python
with rasterio.open(image_tiles[0]) as src:
    tile_rgb = src.read([1, 2, 3]).transpose(1, 2, 0)
    tile_transform, tile_crs = src.transform, src.crs
with rasterio.open(label_tiles[0]) as src:
    tile_labels = src.read(1)
    assert src.transform == tile_transform and src.crs == tile_crs

fig, axes = plt.subplots(1, 2, figsize=(10, 5))
axes[0].imshow(tile_rgb)
axes[0].set_title("Training tile: NAIP RGB")
axes[1].imshow(tile_labels, cmap=LC_CMAP, norm=LC_NORM,
               interpolation="nearest")
axes[1].set_title("Matching Chesapeake classes")
for ax in axes:
    ax.set_axis_off()
plt.tight_layout()
plt.show()
```

**Python reactivation:** `src.read([1, 2, 3])` returns an array with shape `(bands, rows, columns)`. `transpose(1, 2, 0)` rearranges the axes for Matplotlib, which expects `(rows, columns, channels)`.

---

## 5. Train and review a U-Net

### A. Choose a training configuration

We reuse U-Net with a ResNet34 encoder, just as in the previous supervised segmentation pages. The main differences are `num_channels=4` for NAIP and `num_classes=13` for the stored label IDs. We also set `ignore_index=0` so that unlabelled reference pixels do not contribute to the loss.

A short run is useful for learning the workflow; **12 epochs do not guarantee convergence**. Increase the number of epochs for a more meaningful experiment after inspecting the curves.

```{code-cell} python
NUM_EPOCHS = 12      # Try 20 or more for a more complete experiment
BATCH_SIZE = 4       # Reduce on a memory-limited GPU

geoai.train_segmentation_model(
    images_dir=str(TILES_DIR / "images"),
    labels_dir=str(TILES_DIR / "labels"),
    output_dir=str(MODEL_DIR),
    architecture="unet",
    encoder_name="resnet34",
    encoder_weights="imagenet",
    num_channels=4,
    num_classes=13,
    batch_size=BATCH_SIZE,
    num_epochs=NUM_EPOCHS,
    learning_rate=0.001,
    val_split=0.2,
    ignore_index=0,
    seed=42,
    verbose=True,
)
```

```{admonition} A subtlety about class 0
:class: warning
`ignore_index=0` tells the training loss to disregard unlabelled pixels. The network still has 13 output channels because the supplied labels use values 0–12. It is therefore possible for the network to predict class 0; we will count such predictions as errors wherever the **reference** indicates a real land-cover class. For an advanced project, you might instead explicitly remap and exclude void pixels in a custom training pipeline.
```

### B. Inspect training and validation

Use the saved training history to inspect whether the loss is decreasing and validation performance is stabilizing. If training performance keeps improving while validation performance deteriorates, the model may be overfitting.

```{code-cell} python
history_path = MODEL_DIR / "training_history.pth"
model_path = MODEL_DIR / "best_model.pth"
assert history_path.exists(), "Training history not found; run the training cell."
assert model_path.exists(), "Model checkpoint not found; run the training cell."

geoai.plot_performance_metrics(
    history_path=str(history_path), figsize=(14, 4), verbose=True
)
```

The validation figures are useful, but they are *not* the final test of whether the model works on a different geographic area. They also do not replace **class-specific** analysis: a high average score can hide systematic confusion between, for example, low vegetation and shrubland.

---

## 6. Predict the independent test area

### A. Apply the trained model

Now use `geoai.semantic_segmentation()` on the separate test image. **Do not use the test labels as input to this function.** We keep a 512-pixel window and 128 pixels of inference overlap to reduce seams between adjacent windows.

```{code-cell} python
prediction_path = OUTPUT_DIR / "naip_lc_test_prediction.tif"

geoai.semantic_segmentation(
    input_path=str(test_raster_path),
    output_path=str(prediction_path),
    model_path=str(model_path),
    architecture="unet",
    encoder_name="resnet34",
    num_channels=4,
    num_classes=13,
    window_size=512,
    overlap=128,
    batch_size=4,
)

assert prediction_path.exists(), "Prediction raster was not produced."
```

### B. Check geographic alignment

A model can return a raster with an unexpected data type or missing georeferencing. Verify the output before comparing pixels.

```{code-cell} python
with rasterio.open(test_raster_path) as image, \
     rasterio.open(test_landcover_path) as reference, \
     rasterio.open(prediction_path) as predicted:
    assert predicted.shape == image.shape == reference.shape
    assert predicted.crs == image.crs == reference.crs
    assert predicted.transform == image.transform == reference.transform
    prediction = predicted.read(1)
    truth = reference.read(1)

assert set(np.unique(prediction)).issubset(set(range(13)))
print("Predicted class IDs:", np.unique(prediction).tolist())
print("Reference class IDs:", np.unique(truth).tolist())
```

### C. Visual inspection

The most useful first comparison has **three panels**: aerial imagery, reference land cover, and model prediction. Use identical categorical colours in the last two panels so that errors are visible immediately.

```{code-cell} python
fig, axes = plt.subplots(1, 3, figsize=(19, 6))

axes[0].imshow(raster_preview(test_raster_path, [1, 2, 3]))
axes[0].set_title("Aerial image (test area)")

ref_preview = raster_preview(test_landcover_path, [1], categorical=True)
pred_preview = raster_preview(prediction_path, [1], categorical=True)
for ax, data, title in [
    (axes[1], ref_preview, "Reference land cover"),
    (axes[2], pred_preview, "U-Net prediction"),
]:
    ax.imshow(np.ma.masked_where(data == 0, data), cmap=LC_CMAP,
              norm=LC_NORM, interpolation="nearest")
    ax.set_title(title)

for ax in axes:
    ax.set_axis_off()
fig.subplots_adjust(bottom=0.24)
fig.legend(handles=LEGEND, loc="lower center", ncol=4, fontsize=9,
           bbox_to_anchor=(0.5, 0.02))
plt.show()
```

**Look for specific disagreements:** Are roads missed under trees? Are buildings confused with other impervious surfaces? Are narrow wetlands present in the reference but absent from the prediction? For your own project, these patterns often matter more than whether the complete map looks plausible at a glance.

### D. Apply a consistent colour map

For GIS use, write the original reference's class palette into the predicted GeoTIFF. Rasterio can copy the palette directly without changing pixel values or the georeferencing. If the reference palette is not available, the documented colours in Section 3 can be used instead.

```{code-cell} python
with rasterio.open(test_landcover_path) as ref:
    try:
        palette = ref.colormap(1)
    except ValueError:
        palette = {
            i: tuple(int(CLASS_COLORS[i].lstrip("#")[j:j+2], 16)
                     for j in (0, 2, 4)) + (255,)
            for i in range(13)
        }

with rasterio.open(prediction_path, "r+") as dst:
    dst.write_colormap(1, palette)

print("Saved a categorical colour table in:", prediction_path)
```

---

## 7. Evaluate the model

### A. Construct a confusion matrix

The **test reference raster was never used for training**, so it can provide an independent assessment for this specific neighbouring area. A {term}`confusion matrix <Confusion Matrix>` counts how often reference class *i* is predicted as class *j*.

Exclude reference pixels with value **0** (unlabelled). Keep *predicted* zeros in the matrix, because predicting NoData for an actually labelled pixel is still a mistake. We compute all 13 output columns but display only the 12 meaningful reference rows.

```{code-cell} python
N_CLASSES = 13
valid = (truth >= 1) & (truth <= 12)
true_ids = truth[valid].astype("int64")
pred_ids = prediction[valid].astype("int64")

assert np.all((pred_ids >= 0) & (pred_ids < N_CLASSES))
confusion = np.bincount(
    true_ids * N_CLASSES + pred_ids,
    minlength=N_CLASSES * N_CLASSES,
).reshape(N_CLASSES, N_CLASSES)

accuracy = np.trace(confusion) / max(1, confusion.sum())
print(f"Pixel accuracy on labelled test pixels: {accuracy:.2%}")
print(f"Evaluated pixels: {confusion.sum():,}")
```

Visualize **row-normalized** confusion: each row sums to 100% for its reference class, including errors predicted as 0. Large off-diagonal values identify systematic confusion.

```{code-cell} python
rows = confusion[1:13, :]
row_total = rows.sum(axis=1, keepdims=True)
row_pct = np.divide(
    rows * 100.0, row_total,
    out=np.zeros_like(rows, dtype="float64"), where=row_total > 0
)

fig, ax = plt.subplots(figsize=(13, 7))
im = ax.imshow(row_pct, cmap="Blues", vmin=0, vmax=100)
ax.set_xticks(range(13), [str(i) for i in range(13)])
ax.set_yticks(range(12), [f"{i}: {CLASS_NAMES[i]}" for i in range(1, 13)])
ax.set_xlabel("Predicted class ID (0 = NoData)")
ax.set_ylabel("Reference class")
ax.set_title("Test-area confusion matrix: % of each reference class")
fig.colorbar(im, ax=ax, label="Reference-class pixels (%)")
plt.tight_layout()
plt.show()
```

### B. Calculate accuracy metrics

For each class, calculate:

- **Precision:** Of the pixels predicted as that class, how many were correct?
- **Recall:** Of the reference pixels belonging to that class, how many were found?
- **F1:** Harmonic mean of precision and recall.
- **Intersection over Union (IoU):** True positives divided by the union of predicted and reference pixels.

Classes absent from the reference should not be silently assigned an IoU of zero and averaged into the score. We report `NaN` when a class has no reference pixels; the macro average uses only classes present in the test reference.

```{code-cell} python
records = []
for class_id in range(1, 13):
    tp = int(confusion[class_id, class_id])
    support = int(confusion[class_id, :].sum())
    predicted_total = int(confusion[:, class_id].sum())
    fp = predicted_total - tp
    fn = support - tp

    precision = tp / predicted_total if predicted_total else np.nan
    recall = tp / support if support else np.nan
    iou = tp / (tp + fp + fn) if support and (tp + fp + fn) else np.nan
    f1 = 2 * tp / (2 * tp + fp + fn) if support and (2 * tp + fp + fn) else np.nan

    records.append({
        "ID": class_id, "Class": CLASS_NAMES[class_id],
        "Reference pixels": support,
        "Precision": precision, "Recall": recall,
        "F1": f1, "IoU": iou,
    })

metrics = pd.DataFrame(records).set_index("ID")
display(metrics.round(3))
print(f"Macro IoU (classes present in reference): {metrics['IoU'].mean():.3f}")

ax = metrics["IoU"].plot.bar(figsize=(12, 4), color="steelblue")
ax.set_ylabel("IoU")
ax.set_xlabel("Chesapeake class ID")
ax.set_ylim(0, 1)
ax.set_title("Independent test: IoU per land-cover class")
plt.tight_layout()
plt.show()
```

```{admonition} Interpret test scores cautiously
:class: important
The independent test area is a useful **spatial holdout**, but it is nearby and was selected in part for comparable classes. High IoU here does not establish performance across the entire Chesapeake watershed. The Chesapeake reference is itself a land-cover product rather than error-free field observations; acquisition-date differences and ambiguous boundaries can affect the scores.
```

### C. Map incorrect pixels

A compact error map makes the quantitative results geographic. Values 1 denote correctly predicted labelled pixels and 0 denote mistakes. Unlabelled reference pixels are hidden.

```{code-cell} python
correct = (prediction == truth)
error_map = np.ma.masked_where(~valid, correct.astype("uint8"))

fig, axes = plt.subplots(1, 2, figsize=(15, 5))
axes[0].imshow(raster_preview(test_raster_path, [1, 2, 3]))
axes[0].set_title("Test imagery")
# Downsample with nearest neighbour for categorical display.
preview_h = min(600, error_map.shape[0])
step = max(1, int(np.ceil(error_map.shape[0] / preview_h)))
axes[1].imshow(error_map[::step, ::step], cmap=ListedColormap(["tomato", "mediumseagreen"]),
               vmin=0, vmax=1, interpolation="nearest")
axes[1].set_title("Classification agreement: red = wrong, green = correct")
for ax in axes:
    ax.set_axis_off()
plt.tight_layout()
plt.show()
```

**Reflection:** Do errors follow the edges of objects (a boundary problem), or are entire regions assigned the wrong class (a classification problem)? The remedies are different.

---

## 8. Postprocessing

### A. Extract one class

Most project analyses need particular classes—not a polygon for every classified pixel. The earlier draft accidentally vectorized a water-mask variable from a previous chapter. Here we explicitly extract **class 1 (water)** from *this* land-cover prediction. Change `CLASS_TO_VECTORIZE` to another land-cover category to explore the results.

We preserve the original 13-class prediction and create a separate **binary** mask for the selected class. `raster_to_vector()` then converts only the selected pixels into polygons.

```{code-cell} python
CLASS_TO_VECTORIZE = 1  # Water; try 3 for tree canopy
binary_path = OUTPUT_DIR / f"class_{CLASS_TO_VECTORIZE}_binary.tif"
vector_path = OUTPUT_DIR / f"class_{CLASS_TO_VECTORIZE}_polygons.gpkg"

with rasterio.open(prediction_path) as src:
    binary = (src.read(1) == CLASS_TO_VECTORIZE).astype("uint8")
    binary_profile = src.profile.copy()

binary_profile.update(count=1, dtype="uint8", nodata=0, compress="lzw")
with rasterio.open(binary_path, "w", **binary_profile) as dst:
    dst.write(binary, 1)

if binary.any():
    polygons = geoai.raster_to_vector(
        raster_path=str(binary_path),
        output_path=str(vector_path),
        min_area=20,          # square map units; check the CRS below
        simplify_tolerance=None,
    )
    print(f"Extracted {len(polygons)} polygons for {CLASS_NAMES[CLASS_TO_VECTORIZE]}")
else:
    polygons = None
    print("No pixels predicted for the selected class.")
```

### B. Calculate area

Compute area only in an appropriate projected CRS with metre units. Because the teaching data were intended to be projected in metres, the following check should pass. If you adapt this code to a geographic CRS such as EPSG:4326, reproject the polygons before measuring area.

```{code-cell} python
MIN_AREA_M2 = 100

if polygons is not None and not polygons.empty:
    if not polygons.crs or polygons.crs.is_geographic:
        raise ValueError("Reproject polygons to a suitable metric CRS first.")
    polygons = polygons.copy()
    polygons["area_m2"] = polygons.geometry.area
    filtered_polygons = polygons.loc[
        polygons["area_m2"] >= MIN_AREA_M2
    ].copy()
    final_vector_path = OUTPUT_DIR / f"class_{CLASS_TO_VECTORIZE}_filtered.gpkg"
    filtered_polygons.to_file(final_vector_path, driver="GPKG")
    print(f"Retained {len(filtered_polygons)} polygons ≥ {MIN_AREA_M2} m²")
    print("Saved:", final_vector_path)
else:
    filtered_polygons = None
```

Filtering can remove genuine small features. For water, a 100 m² threshold might delete small ponds; for buildings, shape regularization may be more appropriate than smoothing. Choose postprocessing parameters in relation to the feature and the research question, not merely because a map looks cleaner.

### C. Visualize polygons

The final output is now ready for GIS. The visualization below shows predicted polygons over NAIP photography using the same geographic coordinates.

```{code-cell} python
if filtered_polygons is not None and not filtered_polygons.empty:
    geoai.view_vector_interactive(
        filtered_polygons,
        tiles=str(test_raster_path),
        tiles_args={"indexes": [1, 2, 3]},
    )
else:
    print("No polygons remain after filtering. Try another class or threshold.")
```

The GeoPackage can be opened directly in QGIS. Keep the original prediction raster, the extracted binary raster, and the filtered vector layer: each documents a different step in the processing chain.

---

## 9. Common pitfalls

| Pitfall | Why it matters | What to do |
|---|---|---|
| **Image and label misalignment** | Model learns offset boundaries | Compare CRS, transform and image–label overlays before tiling |
| **Treating class 0 as land cover** | Missing reference data become a false category | Ignore reference 0 in loss and evaluation; retain prediction 0 as an error where truth is labelled |
| **Random tile-split optimism** | Nearby patches share spatial structure | Use geographically separate test imagery for final assessment |
| **Only reporting overall accuracy** | Dominant categories hide minority-class errors | Use confusion matrix and per-class IoU |
| **Class imbalance** | Rare classes are easily missed | Inspect distributions; consider class weighting or targeted tiles in an extended experiment |
| **Class IDs with gaps when tiling** | `geoai.export_geotiff_tiles()` renumbers classes to 1, 2, 3, …, so predictions no longer match the reference IDs | Use consecutive class IDs and check the IDs in the label tiles |
| **Imagery and reference from different dates** | Genuine land-cover change looks like model error | Document acquisition dates and inspect changed features |
| **Wrong band order** | Spectral channels no longer match what the model learned | NAIP: Red, Green, Blue, NIR for both train and test |
| **Over-smoothing or aggressive area filtering** | Small real features disappear | Preserve raw raster predictions and justify each postprocessing threshold |

For your own SDS320 project, identify **which classes matter most**, not just how many classes the model predicts. Decide how you will obtain independent labels, whether a spatial holdout is possible, which performance metric suits your research question, and which output features you actually need in vector form.

---

## 10. Mini task

**Time: 10–20 minutes; no retraining required.** Use the already computed `confusion` matrix and `metrics` table.

1. Identify the reference class most commonly predicted as **another** class (ignore class 0 and classes without reference pixels).
2. Report the two class names and the percentage of that reference class affected.
3. Examine the corresponding categories in the aerial image and suggest one possible explanation.
4. Explain one way to improve the model **without using the test labels for training**.

:::{note} Sample solution
:class: dropdown

```python
# Set the correct diagonal to zero, but keep other classes.
off_diagonal = confusion[1:13, 1:13].copy()
np.fill_diagonal(off_diagonal, 0)
row_totals = confusion[1:13, :].sum(axis=1)

# Compare percentage errors, not just absolute pixel counts.
error_rates = np.divide(
    off_diagonal,
    row_totals[:, None],
    out=np.zeros_like(off_diagonal, dtype=float),
    where=row_totals[:, None] > 0,
)
ri, ci = np.unravel_index(np.argmax(error_rates), error_rates.shape)
reference_class = ri + 1
predicted_class = ci + 1
print("Reference:", CLASS_NAMES[reference_class])
print("Predicted:", CLASS_NAMES[predicted_class])
print(f"Confused pixels: {100 * error_rates[ri, ci]:.1f}% of that reference class")
```

The exact two classes depend on your model's actual predictions. For example, a model might confuse *other impervious surfaces* with *roads*, or *scrub/shrub* with *low vegetation*. A defensible next step is to collect more representative training samples of the confused categories, refine the reference labels, or adjust class weights **using only the training area**. Do not repeatedly change the model to maximize its test-set score.

:::

---

## 11. How to improve the model

**Time: about 3 hours, mostly unattended training.** This exercise retrains the model several times. On an Apple-silicon laptop, a short run takes about 2 minutes, Step 2 about 20 minutes, and Steps 3 and 4 about 1 hour each; a GPU runtime is faster.

The evaluation in Section 7 shows a typical pattern for a first multiclass model: a reasonable overall pixel accuracy, but several land-cover classes are never predicted at all, and most of their pixels end up as *tree canopy*. Four causes work together here:

1. **Very little training.** The 2048 × 4096-pixel training image yields only 32 tiles of 512 × 512 pixels. After the validation split, about 25 remain, and with 12 epochs and a batch size of 4 the model sees only around 75 weight updates in total.
2. **Strong class imbalance.** Tree canopy covers about half of the labelled training pixels, while several classes cover less than 1 %. With an unweighted loss, predicting the dominant class when uncertain is the cheapest strategy.
3. **Classes that are hard to separate at 1 m resolution.** *Scrub/shrub* and *tree canopy* look very similar from above, and the three *trees over …* classes describe what lies **under** the canopy, which the image cannot show.
4. **Different class balance in the test area.** Some classes, such as emergent wetlands, are much more common in the test area than in the training area.

In this exercise, you will try four improvements, **one at a time and cumulatively**, so that you can see what each step contributes. Record every run in a results table. Training involves randomness (weight initialisation, validation split, augmentation), so two runs with identical settings rarely give identical scores. Treat small differences between runs with caution.

```{admonition} Use the test area carefully
:class: important
Comparing four runs on the test area is acceptable here because this is a **controlled learning experiment**: you change one factor at a time to understand its effect. In a real project, you would choose between configurations using validation data only and evaluate the final model on the test area **once**. All decisions below (which classes to merge, which class weights to use) must be based on the **training labels only**.
```

### A. Helper functions

The following functions bundle the workflow from Sections 4–7 so that each experiment needs only one call. Every experiment writes its tiles, model and prediction to its own folder in `sds320_landcover/experiments/`, so the baseline results above are not overwritten.

```{code-cell} python
import torch

EXPERIMENT_DIR = WORK_DIR / "experiments"


def remap_labels(src_path, dst_path, mapping):
    """Write a copy of a uint8 label raster with class IDs replaced via `mapping`.

    IDs missing from `mapping` keep their value; 0 (NoData) always stays 0.
    """
    with rasterio.open(src_path) as src:
        labels = src.read(1)
        profile = src.profile.copy()
    lookup = np.arange(256, dtype="uint8")
    for old_id, new_id in mapping.items():
        lookup[old_id] = new_id
    lookup[0] = 0
    Path(dst_path).parent.mkdir(parents=True, exist_ok=True)
    with rasterio.open(dst_path, "w", **profile) as dst:
        dst.write(lookup[labels], 1)
    return Path(dst_path)


def evaluate_prediction(pred_path, labels_path, n_classes=N_CLASSES):
    """Pixel accuracy, macro IoU and per-class IoU on labelled reference pixels."""
    with rasterio.open(labels_path) as ref, rasterio.open(pred_path) as pred:
        truth = ref.read(1).astype("int64")
        predicted = pred.read(1).astype("int64")
    valid = truth > 0
    cm = np.bincount(
        truth[valid] * n_classes + predicted[valid],
        minlength=n_classes * n_classes,
    ).reshape(n_classes, n_classes)

    tp = np.diag(cm)[1:].astype(float)
    support = cm[1:, :].sum(axis=1)
    predicted_total = cm[:, 1:].sum(axis=0)
    union = support + predicted_total - tp
    iou = np.where(support > 0, tp / np.maximum(union, 1), np.nan)
    ids = range(1, n_classes)
    iou = pd.Series(iou, index=ids, name="IoU")
    return {
        "pixel_accuracy": np.trace(cm) / cm.sum(),
        "macro_iou": iou.mean(),  # NaN classes (absent from reference) are skipped
        "classes_found": int((iou > 0).sum()),
        "classes_in_reference": int(iou.notna().sum()),
        "iou": iou,
        "precision": pd.Series(tp / np.maximum(predicted_total, 1), index=ids),
        "recall": pd.Series(np.where(support > 0, tp / np.maximum(support, 1), np.nan), index=ids),
    }


def class_ids(paths):
    """Set of non-zero class IDs occurring in one or more label rasters."""
    ids = set()
    for path in paths:
        with rasterio.open(path) as src:
            ids |= set(np.unique(src.read(1)).tolist())
    return ids - {0}


def run_experiment(name, train_labels, test_labels, num_classes=N_CLASSES,
                   tile_size=512, stride=512, num_epochs=12,
                   class_weights=None, patience=None, seed=42):
    """Tile, train, predict and evaluate one configuration."""
    exp_dir = EXPERIMENT_DIR / name
    tiles_dir, model_dir = exp_dir / "tiles", exp_dir / "model"
    prediction_file = exp_dir / "test_prediction.tif"

    geoai.export_geotiff_tiles(
        in_raster=str(train_raster_path),
        in_class_data=str(train_labels),
        out_folder=str(tiles_dir),
        tile_size=tile_size,
        stride=stride,
        buffer_radius=0,
        skip_empty_tiles=False,
    )
    label_tiles = sorted((tiles_dir / "labels").glob("*.tif"))
    print(f"{name}: {len(label_tiles)} training tiles")
    # export_geotiff_tiles() renumbers class values to 1, 2, 3, ... in sorted
    # order. IDs only survive unchanged if they are already consecutive.
    assert class_ids(label_tiles) <= class_ids([train_labels]), (
        "Tile labels were renumbered: use consecutive class IDs 1, 2, 3, ..."
    )

    geoai.train_segmentation_model(
        images_dir=str(tiles_dir / "images"),
        labels_dir=str(tiles_dir / "labels"),
        output_dir=str(model_dir),
        architecture="unet",
        encoder_name="resnet34",
        encoder_weights="imagenet",
        num_channels=4,
        num_classes=num_classes,
        batch_size=BATCH_SIZE,
        num_epochs=num_epochs,
        learning_rate=0.001,
        val_split=0.2,
        ignore_index=0,
        class_weights=class_weights,
        early_stopping_patience=patience,
        seed=seed,
        verbose=False,
    )

    geoai.semantic_segmentation(
        input_path=str(test_raster_path),
        output_path=str(prediction_file),
        model_path=str(model_dir / "best_model.pth"),
        architecture="unet",
        encoder_name="resnet34",
        num_channels=4,
        num_classes=num_classes,
        window_size=512,
        overlap=128,
        batch_size=4,
    )

    history = torch.load(model_dir / "training_history.pth", weights_only=False)
    result = evaluate_prediction(prediction_file, test_labels, num_classes)
    result["best_val_iou"] = max(history["val_ious"])
    result["epochs_run"] = len(history["val_ious"])
    return result


def results_table(results):
    """Summarise a {name: result} dictionary: scalar metrics and per-class IoU."""
    summary = pd.DataFrame({
        name: {k: v for k, v in r.items() if not isinstance(v, pd.Series)}
        for name, r in results.items()
    }).T
    per_class = pd.DataFrame({name: r["iou"] for name, r in results.items()}).T
    per_class.columns = [f"IoU {i}" for i in per_class.columns]
    return summary, per_class


# The baseline from Sections 5–7 is the first row of the results table.
results = {"0_baseline": evaluate_prediction(prediction_path, test_landcover_path)}
baseline_history = torch.load(history_path, weights_only=False)
results["0_baseline"]["best_val_iou"] = max(baseline_history["val_ious"])
results["0_baseline"]["epochs_run"] = len(baseline_history["val_ious"])

summary, per_class = results_table(results)
display(summary.round(3))
```

### B. Step 1: Merge classes the imagery cannot separate

A coarser class scheme changes **what the model is asked to do**. It is a modelling decision you must justify, not just a trick to raise scores.

1. Use the confusion matrix, the per-class IoU table and the class shares from Section 3F to decide which classes to merge. Write down **one reason per merge**.
2. Define a dictionary `MERGE_MAP = {old_id: new_id, ...}` that assigns **every** original class 1–12 to a new ID, and a matching `MERGED_NAMES` dictionary. The new IDs must be **consecutive** (1, 2, 3, …) and the models need `num_classes = number of merged classes + 1` (for class 0).
3. Use `remap_labels()` to create merged copies of the training labels, the test labels **and the baseline prediction**. Evaluate the unchanged baseline prediction against the merged test labels with `evaluate_prediction(..., n_classes=...)`. This tells you how much the scores change from the new class scheme alone, without any retraining.
4. Now retrain with the short baseline settings: `run_experiment("1_merged", ..., num_classes=...)`. Repeat it with `seed=1` and `seed=2`. Is the retrained model better than the baseline prediction evaluated on merged labels? How large are the differences between the three seeds?
5. Why is the macro IoU of a merged model **not directly comparable** to the 12-class baseline?

```{admonition} Class IDs must be consecutive
:class: warning
`geoai.export_geotiff_tiles()` renumbers the class values it finds to 1, 2, 3, … in sorted order. Labels with gaps, such as 1, 2, 3, 5, 6, 7, 8, are silently turned into 1–7, and the model then predicts the **renumbered** IDs. Comparing such predictions with the original labels produces meaningless scores without any error message. `run_experiment()` therefore checks that the tile labels contain the same IDs as the source raster.
```

### C. Step 2: Create more training tiles

1. Re-run the merged experiment with `tile_size=256` and `stride=128` (name it `"2_more_tiles"`). How many tiles do you get now, compared with 32?
2. Neighbouring tiles now overlap by half. Why are these tiles **not** independent samples? What does the overlap mean for the random validation split, and therefore for `best_val_iou`?

A U-Net is fully convolutional, so a model trained on 256-pixel tiles can still predict with a 512-pixel window.

### D. Step 3: Train longer

1. Re-run Step 2 with `num_epochs=40` (name it `"3_longer"`).
2. Plot the training curves with `geoai.plot_performance_metrics()` for this experiment's `training_history.pth` and compare them with the baseline curves in Section 5B. Was the validation IoU still improving at the last epoch? What would you change if you had more time?

### E. Step 4: Weight the loss by class frequency

1. Compute class frequencies from the **merged training labels** with `class_histogram()`.
2. Derive one weight per merged class, proportional to `1 / sqrt(frequency)`. Give class 0 and classes that do not occur in the training labels a weight of 0, **clip** the weights at 10 and rescale the others to a mean of 1. Pass the weights to `run_experiment(..., class_weights=...)` with otherwise the **same settings as Step 3** (name it `"4_weighted"`).
3. Why use the square root and clipping instead of plain inverse frequency? Compare `results[...]["precision"]` and `results[...]["recall"]` of Steps 3 and 4: what do the weights change, and for which classes? Did weighting improve the result overall?

### F. Compare and reflect

Display the results table with `summary, per_class = results_table(results)` and answer:

1. Which step improved the results most? Did any step make a class **worse**?
2. Which classes still fail, and is the remaining problem more likely the **model**, the **training data** or the **class definition**?
3. What would you try next? Consider more training areas, more epochs, a different loss function (for example Dice or focal loss) and a validation area that is spatially separate from the training tiles.

:::{note} Sample solution
:class: dropdown

```python
# Step 1: merge classes that are not separable in 1 m imagery.
# Every original class gets a new, consecutive ID.
MERGE_MAP = {
    1: 1,                          # water
    2: 2,                          # emergent wetlands
    3: 3, 4: 3, 10: 3, 11: 3, 12: 3,  # tree canopy, scrub/shrub, trees over …
    5: 4,                          # low vegetation
    6: 5,                          # barren
    7: 6,                          # structures
    8: 7, 9: 7,                    # other impervious and roads
}
MERGED_NAMES = {
    0: "NoData", 1: "Water", 2: "Emergent wetlands", 3: "Tree canopy",
    4: "Low vegetation", 5: "Barren", 6: "Structures", 7: "Impervious (incl. roads)",
}
N_MERGED = len(MERGED_NAMES)  # 8, including class 0

merged_dir = EXPERIMENT_DIR / "merged_labels"
merged_train_labels = remap_labels(
    train_landcover_path, merged_dir / "train_landcover.tif", MERGE_MAP
)
merged_test_labels = remap_labels(
    test_landcover_path, merged_dir / "test_landcover.tif", MERGE_MAP
)
baseline_merged = remap_labels(
    prediction_path, merged_dir / "baseline_prediction.tif", MERGE_MAP
)
results["0_baseline_on_merged"] = evaluate_prediction(
    baseline_merged, merged_test_labels, N_MERGED
)

merged = dict(train_labels=merged_train_labels, test_labels=merged_test_labels,
              num_classes=N_MERGED)
for seed in (42, 1, 2):
    results[f"1_merged_seed{seed}"] = run_experiment(
        f"1_merged_seed{seed}", seed=seed, **merged
    )

# Step 2: smaller, overlapping tiles
results["2_more_tiles"] = run_experiment(
    "2_more_tiles", tile_size=256, stride=128, **merged
)

# Step 3: train longer
results["3_longer"] = run_experiment(
    "3_longer", tile_size=256, stride=128, num_epochs=40, **merged
)

# Step 4: class weights from the merged *training* labels only
counts = class_histogram(merged_train_labels)[:N_MERGED].astype(float)
present = counts > 0
present[0] = False  # class 0 is ignored in the loss anyway
frequency = counts / counts[present].sum()
weights = np.zeros(N_MERGED)
weights[present] = np.clip(1 / np.sqrt(frequency[present]), None, 10)
weights[present] /= weights[present].mean()
print(pd.Series(weights.round(2), index=MERGED_NAMES.values()))

results["4_weighted"] = run_experiment(
    "4_weighted", tile_size=256, stride=128, num_epochs=40,
    class_weights=weights.tolist(), **merged
)

summary, _ = results_table(results)
display(summary.round(3))

# Per-class IoU only for runs that use the merged class IDs
merged_runs = {k: v for k, v in results.items() if k != "0_baseline"}
_, per_class = results_table(merged_runs)
per_class.columns = [MERGED_NAMES[i] for i in range(1, N_MERGED)]
display(per_class.round(3))
```

**Reference run.** The table shows one run of this solution on an Apple-silicon laptop. Your numbers will differ: in our repeated short runs, macro IoU varied by up to about 0.06 between seeds.

| Run | Classes | Pixel accuracy | Macro IoU | Epochs |
|---|---:|---:|---:|---:|
| `0_baseline` | 12 | 0.749 | 0.208 | 12 |
| `0_baseline_on_merged` (no retraining) | 7 | 0.775 | 0.367 | – |
| `1_merged` (three seeds) | 7 | 0.758–0.761 | 0.302–0.360 | 12 |
| `2_more_tiles` | 7 | 0.793 | 0.422 | 12 |
| `3_longer` | 7 | **0.840** | **0.504** | 40 |
| `4_weighted` | 7 | 0.818 | 0.462 | 40 |

| IoU per merged class | Water | Wetlands | Tree canopy | Low veg. | Barren | Structures | Impervious |
|---|---:|---:|---:|---:|---:|---:|---:|
| `0_baseline_on_merged` | 0.68 | 0.00 | 0.77 | 0.62 | 0.00 | 0.09 | 0.41 |
| `1_merged_seed42` | 0.75 | 0.00 | 0.76 | 0.59 | 0.00 | 0.11 | 0.31 |
| `2_more_tiles` | 0.30 | 0.40 | 0.84 | 0.62 | 0.00 | 0.31 | 0.49 |
| `3_longer` | 0.62 | 0.42 | 0.88 | 0.66 | 0.00 | 0.38 | 0.56 |
| `4_weighted` | 0.41 | 0.42 | 0.87 | 0.64 | 0.00 | 0.36 | 0.54 |

**Discussion points.**

- **Merging changes the question, not the model.** The unchanged baseline prediction already rises from 0.21 to 0.37 macro IoU, only because the hardest classes no longer count. Retraining on merged labels with the short settings does **not** improve on that (0.30–0.36), and the three seeds differ by almost as much as the configurations. Compare only classes whose definition did not change, such as water, low vegetation, barren and structures.
- **More tiles** give about 15 times as many weight updates per epoch. Emergent wetlands and structures are now learned. Water drops in this run, a sign that 12 epochs are still too few for stable results. Because overlapping tiles are split randomly into training and validation, near-identical pixels appear in both, so `best_val_iou` becomes more optimistic; the test area is unaffected.
- **Longer training** is the largest single improvement. The best validation IoU occurred in the **last** epoch, so even 40 epochs were not enough; more epochs are the most promising next step. Be careful with early stopping on a small, noisy validation set: in a trial with `patience=8`, a weighted run stopped after 17 epochs and performed clearly worse.
- **Class weights did not help here.** After merging, the imbalance is moderate (weights between 0.3 and 2.3). The weights shifted errors between classes (recall for structures rose slightly, recall for water fell from 0.65 to 0.42) without improving the overall result; the difference to Step 3 is within the run-to-run variation. Weighting is not a guaranteed fix, and its effect must be checked per class.
- **Barren is never learned.** The merged training labels contain only about 7,100 barren pixels, compared with 46,000 in the test area. No setting compensates for that: the limitation is the **training data**, and the remedy is more labelled examples, for example from additional training areas.

:::

---

## 12. Key takeaways

- **Multi-class segmentation** predicts a categorical land-cover class at each pixel; this example uses 13 output IDs, including an unlabelled class 0.
- **Reference alignment and NoData handling** are essential. A visually appealing output is not evidence of correct labels.
- **A spatially separate test dataset** allows meaningful evaluation beyond training curves, although it does not establish generalization to every landscape.
- **Per-class IoU and confusion matrices** reveal which land-cover types are reliable and which are systematically confused.
- **GIS-ready outputs** come from deliberately extracting, evaluating and filtering relevant classes while keeping the raw prediction for reproducibility.

### Further reading

- [GeoAI: Train a Land Cover Classification Model](https://opengeoai.org/examples/train_landcover_classification/) — an example following the Chesapeake 13-class segmentation workflow.
- [GeoAI: Training a Segmentation Model](https://opengeoai.org/examples/train_segmentation_model/) — architecture, model checkpoints and training parameters.
- [GeoAI: Loss Functions for Segmentation](https://opengeoai.org/examples/loss_functions_segmentation/) — class weighting and other approaches to class imbalance.
- [Microsoft Planetary Computer: Chesapeake Land Cover](https://planetarycomputer.microsoft.com/dataset/group/chesapeake-lulc) — dataset provenance and description of the 2013–2014 land-cover product.
