---
site:
  outline_maxdepth: 2
---

# The Python ecosystem

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Orienting yourself among the packages this book uses
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why an orientation page

This book uses a fairly large set of Python packages, each covering a specific part of the GeoAI pipeline. You do not need to master all of them now — the goal here is to recognise names when you see them later, and know roughly what each package is responsible for.

```{tip}
If a package name in a later lesson feels unfamiliar, come back to this page rather than guessing from context.
```

---

## 2. Core geospatial data packages

| Package | Purpose |
| --- | --- |
| {term}`GeoPandas` | Vector data as a {term}`GeoDataFrame`: reading, writing, filtering, and spatial operations. |
| {term}`Shapely` | The geometry objects (points, lines, polygons) that GeoPandas is built on. |
| Fiona / Pyogrio | Lower-level vector file I/O engines used behind the scenes by GeoPandas. |
| {term}`Rasterio` | Reading, writing, and reprojecting raster data such as {term}`GeoTIFF` files. |
| Rioxarray | Combines Rasterio with Xarray-style labelled arrays for raster analysis. |
| Xarray | N-dimensional labelled arrays, useful for multi-band or time-series raster data. |
| {term}`GDAL/OGR` | The underlying C library that most of the packages above rely on for format support and reprojection. |
| Pyproj | Coordinate reference system transformations. |

## 3. Mapping and visualisation

| Package | Purpose |
| --- | --- |
| {term}`Leafmap` | Interactive maps in Jupyter notebooks, covered in depth in [L03 – Interactive mapping and visualisation](../03_interactive-mapping-visualisation.md). |
| Folium | The Leaflet.js wrapper that some of Leafmap's functionality builds on. |
| `localtileserver` | Serves large local raster files as map tiles for interactive display. |
| Matplotlib | General-purpose plotting, used throughout this book for static figures. |

## 4. Data access

| Package | Purpose |
| --- | --- |
| `pystac-client` | Searching {term}`SpatioTemporal Asset Catalog (STAC)` catalogues such as the Planetary Computer. |
| `planetary-computer` | Signs and resolves access URLs for {term}`Planetary Computer` assets. |
| `requests` | General-purpose HTTP requests, used for direct REST API access (for example the Swiss geodata APIs in [L02](../02_data-acquisition.md)). |
| `quackosm` / `leafmap.osm` | Querying {term}`OpenStreetMap (OSM)` data by bounding box, place name, or geometry. |
| `boto3` / `s3fs` / `awscli` | Accessing cloud object storage (Amazon S3), used by some open imagery archives. |

## 5. The GeoAI and modelling stack

| Package | Purpose |
| --- | --- |
| `geoai` | This book's central package: helper functions for data access, visualisation, training data preparation, and model workflows. |
| {term}`PyTorch` | The deep learning framework underneath most models used in this book. |
| {term}`TorchGeo` | PyTorch extensions specifically for geospatial data: datasets, samplers, and pretrained models. |
| `torchvision` | General computer vision building blocks (model architectures, transforms) that TorchGeo builds on. |
| `segment-geospatial` (`samgeo`) | Applies the Segment Anything Model to geospatial imagery — covered in [L12 – Segment Anything](../12_segment-anything.md). |

## 6. General scientific Python

| Package | Purpose |
| --- | --- |
| NumPy | Array computation that almost every other package here depends on. |
| Pandas | Tabular {term}`DataFrame` operations; GeoPandas extends this for spatial data. |
| scikit-learn | Classical machine learning algorithms and evaluation metrics. |
| scikit-image | General image processing operations, useful alongside raster-specific tools. |
| tqdm | Progress bars for long-running loops, such as batch downloads or {term}`Batch Inference`. |

---

## 7. A typical import block

Most notebooks in this book will start with some subset of these imports, depending on the task:

```python
# core geospatial
import geopandas as gpd
import rasterio
import xarray as xr

# mapping
import leafmap

# data access
from pystac_client import Client
import planetary_computer

# GeoAI stack
import geoai
import torch
```

You will rarely need all of these at once — a data-acquisition notebook mostly needs the data-access and core geospatial groups, while a modelling notebook in later lessons leans on the GeoAI and modelling stack.

---

## 8. Key takeaways

* This book's packages cluster into five groups: core geospatial data, mapping, data access, GeoAI/modelling, and general scientific Python.
* `geoai`, `leafmap`, `torchgeo`, and `segment-geospatial` are the packages most specific to this book's approach; the rest are widely used across the Python geospatial community.
* You do not need to memorise every package now — treat this page as a reference to return to.
