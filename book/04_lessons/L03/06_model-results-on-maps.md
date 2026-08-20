---
site:
  outline_maxdepth: 2
---

# Model results on maps

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A preview of how you will visualise predictions in later lessons
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why preview this now

You have not trained a model yet — that starts in [L06 – Object detection](../06_object-detection.md) and continues through the modelling lessons. But the visualisation techniques for inspecting model output are the same regardless of which task you are working on, and they build directly on the mapping skills from this lesson. Seeing them now means you will recognise the pattern immediately once you start producing real predictions.

---

## 2. Comparing prediction against input and ground truth

`geoai.plot_prediction_comparison()` places the input image, the model's prediction, and (optionally) the ground truth side by side as static panels — useful for a quick, reproducible sanity check right after inference:

```python
import geoai

geoai.plot_prediction_comparison(
    original_image="input_tile.tif",
    prediction_image="prediction_mask.tif",
    ground_truth_image="ground_truth_mask.tif",
    titles=["Input", "Prediction", "Ground truth"],
)
```

This kind of side-by-side check is where most modelling mistakes first become visible — a systematically shifted mask, a class that is never predicted, or a prediction that ignores an obvious feature in the input.

---

## 3. Detections and segmentation masks on a map

Once a task-specific model produces geographic outputs — bounding boxes, or a georeferenced {term}`Mask` — the same interactive-mapping tools from earlier pages apply directly. A vector of predicted bounding boxes or object outlines is just another {term}`GeoDataFrame`, addable with `add_gdf()`:

```python
import leafmap

m = leafmap.Map()
m.add_raster("input_tile.tif", layer_name="Input imagery")
m.add_gdf(predicted_boxes, layer_name="Predicted objects", style={"color": "red", "fillOpacity": 0})
m.add_layer_control()
m
```

For a quick static overview of many detections at once, `geoai.visualize_multiclass_detections()` draws all predicted boxes on top of the source image with class labels and confidence scores.

---

## 4. Regression outputs

For {term}`Regression` tasks — predicting a continuous value such as canopy height, covered in [L11](../11_regression.md) — `geoai.plot_regression_comparison()` shows the true and predicted rasters side by side with a shared colour scale, and `plot_scatter()` shows predicted against true values as a scatter plot:

```python
geoai.plot_regression_comparison(
    true_raster="ground_truth.tif",
    pred_raster="prediction.tif",
    title="Canopy height (m)",
    cmap="RdYlGn",
)
```

---

## 5. Key takeaways

* Model-output visualisation reuses the same mapping tools introduced earlier in this lesson — nothing new to learn syntactically, just new data.
* `plot_prediction_comparison()` is a fast way to spot-check input, prediction, and ground truth together.
* Predicted boxes or masks are just vector or raster layers, and can be added to an interactive map the same way as any other layer.
* `plot_regression_comparison()` and `plot_scatter()` are the equivalent tools for continuous prediction targets.
