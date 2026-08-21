---
site:
  outline_maxdepth: 1
---

# The GeoAI ecosystem

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
The libraries you may use in SDS320
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this ecosystem matters

You do not need to memorize every package on this page today. You do need to recognize the names when they appear in later lessons, and understand roughly what job each one does, so that when something breaks you know which layer of the stack to look at.

---

## 2. Core idea

A GeoAI pipeline is built from four rough layers: deep learning frameworks, geospatial data libraries, visualization tools, and higher-level packages that tie the others together. Python has a mature library for each layer, and most GeoAI workflows combine various tools.

---

## 3. Main tool families

### 1. Deep learning frameworks

{term}`PyTorch` is the most widely used deep learning framework in research and provides the low-level building blocks for defining and training neural networks. `torchvision` extends it with pre-trained models and image transforms for computer vision. {term}`TorchGeo` is a PyTorch domain library built specifically for geospatial data: it provides datasets, samplers, and pre-trained models that understand multi-spectral inputs and irregularly-shaped satellite scenes, rather than assuming every image is a fixed-size RGB photo the way general computer vision datasets do.

### 2. Geospatial data libraries

{term}`Rasterio` reads and writes raster formats such as {term}`GeoTIFF`, giving you pixel values as NumPy arrays while preserving {term}`CRS <Coordinate Reference System (CRS)>` and other metadata. {term}`GeoPandas` extends Pandas with geometric operations for vector data, and {term}`Shapely` handles the underlying geometry operations (buffering, intersection, union). Underneath most of these sits {term}`GDAL/OGR`, the C/C++ library that actually reads and writes the file formats. A typical workflow uses Rasterio to load imagery and GeoPandas to load vector labels, then combines the two into training data for a model.

### 3. Interactive visualization

{term}`Leafmap` provides a high-level API for interactive maps inside Jupyter, built on backends such as folium and ipyleaflet. It is well-suited to GeoAI work because it can overlay model predictions directly on satellite basemaps and supports side-by-side comparisons, which is useful for visually checking whether a model's output actually makes sense.

### 4. High-level, unified packages

The `geoai` Python package is the central library used throughout this book. It wraps {term}`PyTorch`, {term}`TorchGeo`, {term}`Rasterio`, and {term}`GeoPandas` into a consistent, higher-level interface for tasks such as {term}`object detection <Object Detection>`, {term}`semantic segmentation <Semantic Segmentation>`, and {term}`change detection <Change Detection>`, so you are not stitching together low-level training loops and data loaders by hand for every lesson.

### 5. Segment Anything for geospatial data

The `segment-geospatial` package (also called `samgeo`) adapts Meta's {term}`Segment Anything Model (SAM)` for georeferenced imagery, producing vector outputs such as {term}`GeoJSON` with a proper {term}`CRS <Coordinate Reference System (CRS)>`, rather than plain image masks with no spatial reference. It supports both interactive segmentation (clicking on objects) and automatic mask generation, and is particularly useful for tasks like building or tree-canopy extraction where you have not trained a task-specific model of your own. You will use it directly in [L12 – Segment Anything](../12_segment-anything.md).

---

## 4. A typical workflow stack

A simple project might use the tools like this:

| Workflow step | Possible tool |
| --- | --- |
| Search or download imagery | STAC tools, data APIs, provider portals |
| Read raster data | Rasterio |
| Read vector data | GeoPandas |
| Process geometries | GeoPandas, Shapely |
| Build model-ready chips | Rasterio, NumPy, TorchGeo or GeoAI |
| Train or run a model | PyTorch, GeoAI, samgeo |
| Visualise inputs and outputs | Leafmap, Matplotlib |
| Save outputs | GeoTIFF, GeoPackage, GeoParquet, CSV |
| Document workflow | Jupyter Notebook, README, repository |

This stack may change from project to project. The important part is that each tool has a clear role.

```python
:caption: A preview of what a geoai workflow can look like (not runnable yet)

# This is illustrative only, no coding is required in this lesson.
# You will write real versions of this starting in later lessons.
import geoai

model = geoai.train_segmentation_model(
    images="path/to/image_chips",
    labels="path/to/masks",
    architecture="unet",
)
predictions = model.predict("path/to/new_area.tif")
```

---

## 5. Common pitfalls

- **Trying to install everything at once without an isolated environment.** Deep learning and geospatial packages have many dependencies; a shared, unmanaged environment is a common source of version conflicts.
- **Confusing `geoai` (the package) with GeoAI (the field).** The lowercase, code-formatted name refers specifically to the Python package used in this course.
- **Assuming a high-level package removes the need to understand your data.** `geoai` and `samgeo` handle a lot of plumbing, but they cannot fix a mismatched CRS or a poorly chosen resolution for you.

---

## 6. Mini task

Match each tool below to the layer of the pipeline it belongs to: data I/O, modeling, visualization, or high-level orchestration.

`PyTorch` · `Rasterio` · `Leafmap` · `TorchGeo` · `geoai` · `GeoPandas` · `samgeo`

:::{note} Sample solution
:class: dropdown

- Data I/O: Rasterio, GeoPandas
- Modeling: PyTorch, TorchGeo
- Visualization: Leafmap
- High-level orchestration: `geoai`, `samgeo`

Note that TorchGeo sits between data I/O and modeling in practice, since its samplers handle geospatial-aware data loading specifically for model training, but its core purpose is preparing data for PyTorch models rather than reading files from disk in the first place.
:::

---

## 7. Further reading

- [PyTorch documentation](https://pytorch.org)
- [TorchGeo documentation](https://torchgeo.readthedocs.io)
- [Rasterio documentation](https://rasterio.readthedocs.io)
- [GeoPandas documentation](https://geopandas.org)
- [Leafmap documentation](https://leafmap.org)
- [segment-geospatial (samgeo) documentation](https://samgeo.gishub.org)

---

## 8. Key takeaways

- GeoAI pipelines combine four layers: deep learning frameworks, geospatial data libraries, visualization tools, and high-level orchestration packages.
- `PyTorch` and `TorchGeo` handle modeling; `Rasterio`, `GeoPandas`, and `Shapely` handle data; `Leafmap` handles visualization; `geoai` and `samgeo` tie these together at a higher level.
- Your SDS210 skills with Rasterio and GeoPandas carry over directly; this course adds the modeling layer on top.
- A clean, isolated environment prevents most of the dependency headaches that come with combining geospatial and deep learning packages.
