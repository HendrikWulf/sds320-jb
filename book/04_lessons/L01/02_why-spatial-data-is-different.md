---
site:
  outline_maxdepth: 2
---

# Why spatial is special

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
The properties of geospatial data that break default machine learning assumptions
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Nearby things are related

Most machine learning methods assume observations are independent. Spatial data rarely is: two neighbouring pixels or points are usually more similar to each other than two randomly chosen ones. This property is called {term}`Spatial Autocorrelation`.

This matters very concretely. If you split training and validation data randomly across a study area, nearby pixels can end up on both sides of the split — the model then partly "sees" the validation area during training through its neighbours, and your reported accuracy will look better than the model actually performs on genuinely new locations. Later lessons on {term}`Training Data` and evaluation return to this problem in more depth.

---

## 2. Scale and resolution decide what is visible

A satellite image at 10 m {term}`Spatial Resolution` and one at 0.5 m resolution can show the same location, but only one of them can resolve individual cars, or thin footpaths, or single tree crowns. Choosing a task without checking whether the resolution can actually support it is a common early mistake.

Scale works the other way too: a pattern that is meaningful at a city level (urban sprawl) may be invisible or meaningless at a single-building level, and vice versa. A GeoAI task should be matched to a resolution and extent where the pattern of interest is actually observable.

```{tip}
Before you commit to a modelling task, ask: at the resolution of my data, can a human expert actually see the pattern I want the model to learn? If not, the model will struggle too.
```

---

## 3. A raster is a grid with a location, not just an array

A {term}`GeoTIFF` or similar raster is not just a matrix of numbers — every pixel corresponds to a real location, defined through a {term}`Coordinate Reference System (CRS)` and a geotransform. Two rasters covering the "same" area can be silently misaligned if their CRS, resolution, or pixel grid differ. Reprojecting, resampling, and checking alignment are unavoidable steps in almost any GeoAI workflow, well before any model is involved.

Vector data carries the same requirement: a {term}`GeoDataFrame` of building footprints is only meaningful together with its CRS, and combining vector and raster data safely requires both to be interpreted in the same reference system.

---

## 4. Class and event imbalance is often extreme

Spatial phenomena are frequently rare relative to the area they occur in: burned areas after a wildfire, individual landslides, specific crop types in a large region. A dataset that is 99% background and 1% target class is common, and it changes how a model should be trained and evaluated — plain {term}`Accuracy` becomes a misleading metric, which is why GeoAI work often relies on metrics such as {term}`Intersection over Union (IoU)`, {term}`Precision`, {term}`Recall`, or {term}`F1-score` instead.

---

## 5. Edge effects and tiling introduce artefacts

Large rasters are usually too big to process at once and are split into smaller {term}`Tile` or {term}`Chip` units for training and {term}`Inference`. Objects that straddle a tile boundary can be cut in half, and predictions near tile edges are often less reliable than predictions near the centre. Understanding this now will make the {term}`Tiled Inference` workflows in later lessons much easier to reason about.

---

## 6. Key takeaways

* Spatial data violates the independence assumption behind most machine learning methods — see {term}`Spatial Autocorrelation`.
* Resolution and scale determine what a model can realistically detect.
* Every raster and vector layer carries a {term}`Coordinate Reference System (CRS)` that must be checked, not assumed.
* Class imbalance is common in spatial data and changes which evaluation metrics are meaningful.
* Tiling large data for processing introduces edge effects that later lessons address directly.
