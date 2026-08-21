---
site:
  outline_maxdepth: 2
---

# Why spatial is special

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
The properties of spatial data that make GeoAI its own field
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why these properties matter

A photograph from a phone and a satellite image chip can both be stored as arrays of pixel values, and a generic image classifier could technically be pointed at either one. But treating them the same way is a common source of quiet, expensive mistakes in student projects: an accuracy estimate that looks great until you realize the test pixels were sitting right next to the training pixels, or a model trained on one region that silently fails on another because nobody accounted for a difference in resolution.

---

## 2. Core idea

Spatial data carries structure that ordinary images do not. That structure is not a nuisance to work around; it is information you can use, but only if your workflow is built to respect it.

---

## 3. Key properties

Walk through each property below and ask, for your own emerging project idea, whether it applies.

### 1. Coordinate reference systems and projections

Every geospatial dataset is tied to a location on Earth through a {term}`coordinate reference system <Coordinate Reference System (CRS)>` ({abbr}`CRS (Coordinate Reference System)`). Because the Earth is a three-dimensional shape and a map is flat, representing it always involves a {term}`projection`, and every projection distorts something (area, distance, direction, or shape). If you combine datasets in different CRSs without {term}`reprojecting <Reprojection>` them onto a shared one, distances and overlaps will simply be wrong.

### 2. Spatial resolution and scale

Sensors capture data at very different {term}`spatial resolutions <Spatial Resolution>`. {term}`Sentinel-2` provides 10-meter pixels; some commercial satellites resolve below a meter. The same object looks completely different depending on the sensor, and a model trained at one resolution often does not transfer well to another. Before you settle on a data source, ask whether the resolution is fine enough to actually see the thing you care about.

### 3. Spectral bands

Unlike an RGB photo with three color channels, satellite imagery often carries many {term}`spectral bands <Spectral Band>`, spanning visible, near-infrared, and shortwave infrared wavelengths. These extra bands carry information invisible to the human eye, such as vegetation stress, visible in the {abbr}`NIR (Near-Infrared)` band, or soil moisture. A model that only looks at {term}`multispectral imagery <Multispectral Imagery>`'s RGB channels is throwing away signal that an RGB-only computer vision model was never built to use in the first place.

### 4. The temporal dimension

Satellites revisit the same location on a regular cycle, which creates dense time series rather than one-off snapshots. That enables tasks like {term}`change detection <Change Detection>` and monitoring of seasonal or gradual processes, but it also means your model, and your evaluation, need to reason about sequences of images, not just a single one.

### 5. Spatial autocorrelation

Nearby locations tend to be more similar than distant ones. This has a very concrete consequence: if you split pixels from a single scene into training and test sets at random, pixels that end up on opposite sides of that split can still be right next to each other on the ground, so your test set is not really independent of your training set. That produces {term}`accuracy` estimates that look better than the model will actually perform elsewhere. Proper evaluation usually requires spatial separation between training and test regions, not a random shuffle.

### 6. Diverse data formats

Raster data alone comes in formats such as {term}`GeoTIFF`, {term}`Cloud Optimized GeoTIFF (COG)`, and {term}`Zarr`, each with different conventions for metadata and multi-band storage. Vector data adds formats such as {term}`GeoJSON`, {term}`Shapefile <Shapefile>`, and {term}`GeoPackage`. Many GeoAI tasks need both at once, for example using vector building outlines as labels for a raster-based model, which means part of your workflow will always be format conversion and reconciling {term}`CRS <Coordinate Reference System (CRS)>` differences.

### 7. Large file sizes and tiled processing

A single {term}`Sentinel-2` {term}`scene` can cover a 100-by-100-kilometer area with well over a hundred million pixels per band. Processing a whole scene at once usually will not fit in memory or on a {term}`GPU`. GeoAI workflows instead cut imagery into {term}`chips <Chip>` or {term}`tiles <Tile>`, run the model on each one, and stitch the results back together, which introduces its own edge-effect problems at tile boundaries.

```{admonition} Tobler's First Law of Geography
:class: note
"Everything is related to everything else, but near things are more related than distant things." This is the intuition behind spatial autocorrelation, and it is worth remembering every time you are tempted to split data with a plain random shuffle. See the [Wikipedia article on Tobler's First Law](https://en.wikipedia.org/wiki/Tobler%27s_first_law_of_geography) if you want the fuller background.
```

---

## 4. Python reactivation

You already handled {term}`CRS <Coordinate Reference System (CRS)>` transforms and raster/vector I/O in SDS210 with {term}`Rasterio` and {term}`GeoPandas`. Nothing here is new machinery, it is a reminder of why those steps exist. When you see `.to_crs()` or a resampling call later in the course, connect it back to the properties above rather than treating it as boilerplate.

---

## 5. Common pitfalls

- **Random train/test splits on a single scene.** This almost always inflates your reported accuracy because of spatial autocorrelation. Split by region, not by pixel.
- **Mixing resolutions without checking.** Combining a 10-meter and a 30-centimeter dataset without deciding on a common resolution first can silently break alignment.
- **Ignoring the CRS until something looks wrong on a map.** Check and standardize CRS early, not as a debugging step after your analysis has already gone sideways.

---

## 6. Mini task

Take your rough project idea from the [lesson landing page](../01_introduction-to-geoai.md). List which two or three of the seven properties above will matter most for it, and explain why in one sentence each.

:::{note} Sample solution
:class: dropdown

For a project mapping informal settlement growth in a fast-growing city: spatial resolution matters most, because small, closely-packed structures require sub-meter or near-meter imagery to be visible at all. The temporal dimension matters second, because "growth" is inherently a comparison across at least two dates. Spatial autocorrelation matters for evaluation, because neighborhoods look visually similar block to block, so a random pixel split would overstate how well the model generalizes to a different part of the city.
:::

---

## 7. Key takeaways

- Spatial data differs from ordinary images in CRS, resolution, spectral bands, temporal depth, autocorrelation, formats, and file size.
- These are not obstacles to route around; they are structure that a good GeoAI workflow uses deliberately.
- Spatial autocorrelation has a direct, practical consequence: naive random data splits produce overly optimistic accuracy estimates.
- Before choosing a data source for your project, check whether its resolution, bands, and temporal coverage actually match what your question needs.