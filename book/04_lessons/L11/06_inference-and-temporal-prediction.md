---
site:
  outline_maxdepth: 2
---

# Inference and predictions

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Running inference and predicting continuous values over time
</div>
<!-- markdownlint-enable MD033 -->

---

You trained a model on the previous page. This page applies it across full rasters, first the training-year scene to check it against known values, then a different year entirely, to see whether it generalizes across time.

---

## 1. Motivation

A model that only works on the exact tiles it was trained on is not very useful. Running wall-to-wall inference, and specifically testing it on imagery from a year it never saw, is what tells you whether the model actually learned a generalizable mapping from imagery to NDVI, rather than memorizing its specific training scene.

---

## 2. Core idea

`geoai.predict_raster()` runs the same kind of {term}`tiled inference <Tiled Inference>` you have seen in earlier lessons, sliding a window across a full raster and stitching results together, here using overlap with Gaussian-weighted blending to avoid visible seams in a continuous-valued output, where any hard edge between tiles would be immediately obvious.

---

## 3. Workflow

### A. Run inference on the training area

```{code-cell} python
geoai.predict_raster(
    model=model,
    input_raster=train_raster,
    output_raster="ndvi_model/predicted_ndvi_2022.tif",
    tile_size=256,
    overlap=64,
    batch_size=8,
    clip_range=(-1.0, 1.0),
)
```

Running inference on the 2022 training raster first, even though the model already saw this imagery during training, lets you evaluate predictions against the known 2022 NDVI ground truth on the next page, a useful check before trusting the model on genuinely new imagery.

### B. Overlap, blending, and clipping

`overlap=64` means adjacent tiles share 64 pixels; `predict_raster()` blends predictions in these shared regions using Gaussian weighting, which favors each tile's more reliable center over its edges, smoothing out the transition rather than creating a visible seam at every tile boundary. `clip_range=(-1.0, 1.0)` enforces NDVI's valid physical range on the output, the inference-time counterpart to the `target_min`/`target_max` filtering applied during tile creation two pages ago.

```{tip}
An overlap of 64 pixels or more is a reasonable starting point for most continuous-output tasks. If you notice faint grid-like patterns in a prediction map, increasing overlap is usually the first thing worth trying.
```

### C. Predict on new imagery

```{code-cell} python
geoai.predict_raster(
    model=model,
    input_raster=test_raster,
    output_raster="ndvi_model/predicted_ndvi_2023.tif",
    tile_size=256,
    overlap=64,
    batch_size=8,
    clip_range=(-1.0, 1.0),
)
```

This is the same function call, applied to `test_raster`, the 2023 Landsat scene the model never saw during training. If the model has learned a genuine relationship between spectral bands and NDVI, rather than memorizing specifics of the 2022 scene, it should produce plausible NDVI predictions for 2023 as well.

### D. Visualize the temporal prediction

```{code-cell} python
geoai.visualize_prediction(
    input_raster=test_raster,
    pred_raster="ndvi_model/predicted_ndvi_2023.tif",
    cmap="RdYlGn",
    vmin=-0.2,
    vmax=0.8,
)
```

Displaying the 2023 input imagery alongside its predicted NDVI map lets you visually check whether the predicted vegetation patterns make sense relative to what the imagery actually shows, greener areas predicted as higher NDVI, built-up or bare areas predicted as lower, before treating the result as usable.

---

## 4. Python reactivation

Nothing new syntactically here; this page reuses the same function-call pattern from earlier pages, applied first to familiar imagery and then to new imagery, with `input_raster` as essentially the only thing that changes between steps A and C.

---

## 5. Common pitfalls

- **Skipping `clip_range` at inference.** Without it, predictions near tile edges or in unusual regions can fall outside the physically valid range, exactly the kind of implausible value clean data preparation was meant to prevent.
- **Assuming temporal generalization always works.** It depends on whether the relationship between imagery and target is stable across the two dates; always visually check a new-year prediction rather than assuming it transferred correctly.
- **Setting `overlap` too low.** This risks visible seams or inconsistent values at tile boundaries in the stitched output, especially for smoothly varying continuous targets.
- **Skipping the training-year inference check in step A.** Running inference on the training year first gives you a sanity check against known values before trusting the model on genuinely new data.

---

## 6. Mini task

The 2023 prediction map shows a cluster of pixels with unusually high NDVI values in an area that appears, in the input imagery, to be a parking lot. List two possible explanations, and how you might investigate which applies.

:::{dropdown} Sample solution
:class: note

One possibility is a genuine model error: the model may have learned spurious associations that do not hold for this specific location or year, especially plausible if this area looked different (perhaps vegetated) in the 2022 training imagery. Another possibility is that the input imagery itself has an issue at that location, cloud contamination, a processing artifact, or misregistration, causing the model to receive misleading input. To investigate, you would compare the 2022 and 2023 imagery at that specific location directly, and check whether the anomaly is isolated to a few pixels (more consistent with noise or a local artifact) or covers a broader, spatially coherent area (more consistent with a genuine, if surprising, model behavior worth understanding further).
:::

---

## 7. Key takeaways

- `predict_raster()` runs wall-to-wall inference with overlap and Gaussian-weighted blending, producing seamless continuous-valued output.
- `clip_range` enforces a target's valid physical range on predictions, the inference-time counterpart to tile-time filtering.
- Testing a model on a year it never saw during training is a direct check of temporal generalization, not just interpolation within familiar data.
- Visual inspection against the source imagery remains necessary even for a well-trained model; plausibility checks catch problems aggregate metrics alone might miss.
