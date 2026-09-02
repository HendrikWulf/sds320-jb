---
site:
  outline_maxdepth: 2
---

# Multiclass segmentation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Move from binary masks to thematic maps and GIS-ready spatial features
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

Every workflow so far has predicted one of two classes. This page extends the same pattern to a full, multi-class land-cover scheme, and closes out the practical side of this lesson with the post-processing steps that turn a raster prediction into something you can actually use in QGIS.

Most land-cover mapping projects need more than "target class versus background." Learning what actually changes, and what does not, when you move from 2 classes to 13 lets you extend everything from the earlier pages to your own multi-class project.

---

## 2. Core idea

Multi-class segmentation is the same underlying task as binary segmentation, with two differences: `num_classes` increases from 2 to however many categories your scheme defines, and your label rasters must store an integer class value (0, 1, 2, and so on) at every pixel, rather than a simple 0/1 mask. The loss function and evaluation metrics then operate across all classes simultaneously, and per-class results become essential reading, not an optional extra, exactly the lesson from evaluating detectors in L06.

---

## 3. Workflow

### A. Inspect labels before training

Multi-class training data can contain more subtle errors than binary masks. Typically, you should check that the class values are valid and that the imagery and labels are aligned. Ensure that visually similar classes are consistently separated and that rare classes are present. Check that NoData and background values are handled correctly.

This page uses the {term}`Chesapeake Land Cover` scheme, part of the broader {term}`land cover classification <Land Cover Classification>` task, which defines 13 land-cover categories (water, tree canopy, impervious surfaces, and so on) for the Chesapeake Bay watershed, paired with NAIP 4-band imagery. Class 0 represents background or no-data, and classes 1 through 12 represent the actual land-cover types.

The source workflow compares the label map and imagery using a split view:

```{code-cell} python
legend_args = {
    "builtin_legend": "Chesapeake",
    "title": "Land Cover Type",
}

geoai.create_split_map(
    left_layer=train_landcover_path,
    right_layer=train_raster_path,
)
```

A {term}`Split-Panel Map` is particularly useful for checking whether pixel labels correspond to the imagery.

### B. Create the training tiles

The workflow remains familiar:

```{code-cell} python
out_folder = "landcover"

tiles = geoai.export_geotiff_tiles(
    in_raster=train_raster_path,
    out_folder=out_folder,
    in_class_data=train_landcover_path,
    tile_size=512,
    stride=256,
    buffer_radius=0,
)
```

This is the same `export_geotiff_tiles()` call used for the binary building workflow; the only conceptual difference is that `train_landcover_path` now points to an integer-valued, 13-class label raster instead of a binary mask.

### C. Train the multi-class model

The source example uses four-band NAIP imagery and 13 output classes:

```{code-cell} python
geoai.train_segmentation_model(
    images_dir=f"{out_folder}/images",
    labels_dir=f"{out_folder}/labels",
    output_dir=f"{out_folder}/unet_models",
    architecture="unet",
    encoder_name="resnet34",
    encoder_weights="imagenet",
    num_channels=4,
    num_classes=13,
    batch_size=8,
    num_epochs=20,
    learning_rate=0.001,
    val_split=0.2,
)
```

The call is identical in shape to the binary examples, with `num_classes=13` as the key change. With more classes to distinguish, some of them spectrally similar (deciduous forest and cropland can look alike at certain times of year), the model generally needs sufficient training time to learn the subtler distinctions, so multi-class problems often benefit from longer training runs than an equivalent binary task.

```{admonition} Watch for class imbalance
:class: warning
Multi-class land-cover data is often heavily imbalanced: common classes like tree canopy may occupy far more pixels than rare ones like wetlands. This {term}`class imbalance <Class Imbalance>` can make aggregate metrics look strong while the model performs poorly on exactly the rare classes a project cares about. Common remedies include class-weighted loss functions, focal loss, and deliberately oversampling tiles that contain rare classes.
```

### D. Evaluate per class

The evaluation call is the same `plot_performance_metrics()` used earlier in this lesson, but for multi-class problems, reading the per-class breakdown matters even more than it did for detection in L06: an aggregate IoU can look acceptable while masking a specific class, often a rare or spectrally ambiguous one, performing poorly. If a specific class matters most for your project, check its individual performance directly rather than relying on the overall number.

### E. Run inference

Apply the trained model to an unseen raster:

```{code-cell} python
masks_path = "landcover_prediction.tif"
model_path = f"{out_folder}/unet_models/best_model.pth"

geoai.semantic_segmentation(
    input_path=test_raster_path,
    output_path=masks_path,
    model_path=model_path,
    architecture="unet",
    encoder_name="resnet34",
    num_channels=4,
    num_classes=13,
    window_size=512,
    overlap=128,
    batch_size=4,
)
```

The output raster stores the predicted class for every pixel.

### F. Evaluate class-specific failures

Categorical predictions become easier to interpret when the same colour mapping is used for reference and prediction. The source workflow uses:

```{code-cell} python
geoai.write_colormap(
    masks_path,
    train_landcover_path,
    output=masks_path,
)
```

`write_colormap()` copies the same color scheme used for the training labels onto the predicted raster, so the two can be compared visually at a glance rather than needing a separate legend for each.

```{code-cell} python
geoai.view_raster(
    masks_path,
    basemap=test_raster_path,
    legend_args=legend_args,
)
```

A consistent {term}`Colormap` helps visual comparison, but colours do not replace quantitative evaluation. For multi-class segmentation, inspect not only whether a pixel is wrong but **which classes are confused**. A {term}`Confusion Matrix` can reveal these class-to-class errors if suitable evaluation code and reference data are available. Per-class IoU is also useful.

### G. Convert a class to vector output

For most downstream uses, you will vectorize one class at a time rather than the entire multi-class raster at once. For a binary water mask, the source workflow uses:

<!-- TODO: check the mask: s2_mask or output_path=masks_path, . -->

```{code-cell} python
# Sometimes you need individual polygons from a segmentation output
gdf = geoai.raster_to_vector(
    raster_path=s2_mask,
    output_path="landcover_class.geojson",
    min_area=100,
    simplify_tolerance=None,
)

# Smooth irregular boundaries
gdf = geoai.smooth_vector(
    gdf, 
    smooth_iterations=3,
    output_path="water_smoothed.geojson",
)

# Calculate geometric properties
gdf_props = geoai.add_geometric_properties(
    gdf, area_unit="m2", 
    length_unit="m"
)

# Filter small polygons
gdf_filtered = gdf_props[
    gdf_props["area_m2"] > 1000
]

# Regularize building geometry
gdf = geoai.orthogonalize(
    masks_path,
    "buildings.geojson",
    epsilon=2,
)
```

`smooth_vector()` softens the blocky, pixel-aligned edges that raw raster-to-vector conversion tends to produce, giving more natural-looking boundaries, a lighter-weight alternative to the full orthogonalization used for buildings in an earlier page. As before, `add_geometric_properties()` lets you filter out small, likely spurious polygons using an area or shape threshold before treating the result as finished.

---

## 4. Common pitfalls

- **Reporting only an aggregate metric for a 13-class problem.** As with detection, per-class results are where you find out whether the classes you actually care about are being handled well.
- **Under-training a multi-class model.** More classes generally means more for the model to learn; the number of epochs that worked for a binary task may not be enough here.
- **Forgetting to reapply the colormap after generating a new prediction.** Without `write_colormap()`, a multi-class raster displays as an arbitrary, unhelpful color ramp rather than a meaningful thematic map.
- **Vectorizing every class at once when only one matters.** Vectorizing and filtering a single class of interest, as in step E, is usually more useful than converting the entire multi-class raster indiscriminately.

---

## 5. Mini task

Your 13-class land-cover model reports an overall validation IoU of 0.75, which sounds reasonable. Per-class IoU shows tree canopy at 0.88 and wetlands at 0.31. Your project specifically needs to track wetland change over time. What does the overall number hide, and what would you do next?

:::{dropdown} Sample solution
:class: note

The overall IoU of 0.75 is dominated by well-performing, likely common classes such as tree canopy, and hides the fact that wetlands, the class the project actually depends on, perform far worse at 0.31. Given this, the reasonable next step is not to accept the model as-is, but to investigate why wetlands underperform (likely candidates include class imbalance, given wetlands are probably a minority class, and spectral ambiguity with adjacent classes), and consider remedies such as a class-weighted loss, oversampling wetland-containing tiles, or adding more wetland-labeled training data before relying on the model for the project's actual deliverable.
:::

---

## 6. Key takeaways

- Multi-class segmentation changes `num_classes` and requires integer-valued label rasters; the rest of the workflow is unchanged from binary segmentation.
- Class imbalance is common in land-cover data and can hide poor performance on rare classes behind a strong aggregate metric.
- Per-class evaluation is essential for multi-class problems, not an optional detail, especially for whichever class your project actually depends on.
- Vectorizing and filtering one class of interest at a time is usually more useful than converting an entire multi-class raster indiscriminately.

---

:::{admonition} Optional: publishing segmentation models to Hugging Face Hub
:class: dropdown

Sharing a trained segmentation model through the {term}`Hugging Face Hub` works the same way it did for detectors in L06. This is optional, advanced material, useful if you plan to share a model publicly, but it is not required for the core segmentation workflow.

```{code-cell} python
from huggingface_hub import notebook_login

notebook_login()
```

`push_timm_model_to_hub()` uploads the trained weights and configuration (architecture, encoder, channel count, number of classes) to a repository you specify:

```{code-cell} python
repo_url = geoai.push_timm_model_to_hub(
    model_path=model_path,
    repo_id="your-username/chesapeake-landcover-unet-resnet34",
    encoder_name="resnet34",
    architecture="unet",
    num_channels=4,
    num_classes=13,
)
```

`timm_segmentation_from_hub()` then runs sliding-window inference directly from a hosted model, without needing a local checkpoint or manual architecture specification:

```{code-cell} python
geoai.timm_segmentation_from_hub(
    input_path=test_raster_path,
    output_path="landcover_hub_prediction.tif",
    repo_id="your-username/chesapeake-landcover-unet-resnet34",
    window_size=512,
    overlap=128,
    batch_size=4,
)
```

:::

## Further reading

- OpenGeoAI, ["Loss Functions for Segmentation"](https://opengeoai.org/examples/loss_functions_segmentation/) — practical guidance on class-weighted and focal loss options for imbalanced multi-class problems like the one on this page.
