---
site:
  outline_maxdepth: 2
---

# Working with remote sensing data

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Searching and inspecting imagery before it enters your project workflow
</div>
<!-- markdownlint-enable MD033 -->

## Why this matters

In the previous page, you defined search criteria for suitable data. Here, you apply that logic to remote sensing data.

Remote sensing datasets can be powerful for SDS320 projects, but they come with trade-offs. Spatial resolution determines which objects are visible. Temporal coverage determines whether you can observe the period of interest. Cloud cover affects usability. Spectral bands determine which surface properties you can analyse.

This page focuses on search and inspection, not full preprocessing. Preprocessing comes later in the course.

---

## Core idea

Modern remote sensing data access often uses STAC.

{term}`SpatioTemporal Asset Catalog (STAC)` is a standard way to describe and search geospatial assets. Instead of learning a completely different search system for every provider, you can use a similar logic across many archives.

A typical STAC workflow is:

```text
Catalog
→ Collection
→ Item
→ Asset
```

| STAC level | Meaning                                                                  |
| ---------- | ------------------------------------------------------------------------ |
| Catalog    | Entry point to a data archive or API.                                    |
| Collection | A dataset group, such as Sentinel-2 or Landsat.                          |
| Item       | One scene, tile or observation with time and footprint.                  |
| Asset      | One file connected to an item, such as a band, preview or metadata file. |

---

## Workflow

### Step 1: Choose the imagery source

Common open or partly open imagery sources include:

| Source           | Typical use                                                              |
| ---------------- | ------------------------------------------------------------------------ |
| Sentinel-2       | Multispectral land monitoring, vegetation, land cover, change detection. |
| Landsat          | Long-term change analysis over decades.                                  |
| NAIP             | High-resolution aerial imagery for the United States.                    |
| SWISSIMAGE       | Very high-resolution imagery for Switzerland.                            |
| Vantor Open Data | Event-focused high-resolution imagery for selected disasters.            |

Choose based on your project needs, not only on popularity.

```{tip}
For a long time series, Landsat may be more useful than a higher-resolution but shorter archive. For small objects, moderate-resolution imagery may not be enough.
```

---

### Step 2: Connect to a STAC catalog

The Microsoft Planetary Computer hosts major Earth observation collections and provides a STAC API. The GeoAI source material uses `pystac_client` to search the catalog.

```{code-cell} python
from pystac_client import Client

catalog = Client.open("https://planetarycomputer.microsoft.com/api/stac/v1")

print(catalog.title)
```

Online catalogs change over time. Collection counts, item IDs and asset names may differ from examples.

---

### Step 3: Inspect collections

Before searching for items, inspect the collection metadata.

```{code-cell} python
collection = catalog.get_collection("sentinel-2-l2a")

print("Title:", collection.title)
print("License:", collection.license)
print("Temporal extent:", collection.extent.temporal.intervals)
print("Spatial extent:", collection.extent.spatial.bboxes)
```

This helps you check whether the collection could fit your project before requesting scenes.

---

### Step 4: Search for items

A search usually filters by:

* collection,
* bounding box,
* date range,
* cloud cover or another quality property,
* maximum number of items.

```{code-cell} python
bbox = [8.50, 47.35, 8.60, 47.42]  # example area near Zurich, WGS84 lon/lat

search = catalog.search(
    collections=["sentinel-2-l2a"],
    bbox=bbox,
    datetime="2025-06-01/2025-08-31",
    query={"eo:cloud_cover": {"lt": 20}},
    max_items=5,
)

items = search.item_collection()
print(f"Found {len(items)} item(s)")
```

```{warning}
The example uses a small bounding box. Do not begin with a whole country or large city unless you know the data volume and processing consequences.
```

---

### Step 5: Inspect items and assets

After a search, inspect the returned items before downloading or loading data.

```{code-cell} python
if len(items) > 0:
    item = items[0]

    print("Item ID:", item.id)
    print("Date:", item.datetime)
    print("Cloud cover:", item.properties.get("eo:cloud_cover"))
    print("Platform:", item.properties.get("platform"))

    print("\nAssets:")
    for key, asset in item.assets.items():
        print(key, "-", asset.title)
```

Assets are the actual files connected to the item. For Sentinel-2, assets may include individual spectral bands, previews, scene classification layers and metadata.

Select assets based on your analytical need. For example, vegetation analysis usually needs red and near-infrared bands. A visual overview may only need a true-colour preview.

---

## SWISSIMAGE via STAC

For projects in Switzerland, SWISSIMAGE can be relevant because it provides very high-resolution imagery. The provided SWISSIMAGE notes use the swisstopo STAC API and highlight an important CRS pattern:

```text
Search bbox: WGS84 coordinates
Loaded data: often Swiss projected CRS, such as EPSG:2056
```

The source notes use this structure:

```{code-cell} python
from pystac_client import Client

STAC_URL = "https://data.geo.admin.ch/api/stac/v0.9/"
catalog = Client.open(STAC_URL)

bbox_wgs84 = [8.5300, 47.3700, 8.5400, 47.3800]  # central Zurich
collection_id = "ch.swisstopo.swissimage-dop10"

search = catalog.search(
    collections=[collection_id],
    bbox=bbox_wgs84,
)

items = list(search.items())
print(f"Found {len(items)} image tile(s)")
```

:::{caution} High-resolution data can become large quickly
:class: dropdown

SWISSIMAGE at 10 cm resolution can create very large arrays even for small areas. The supplied notes recommend chunked loading with tools such as `odc-stac` to avoid memory problems.

Only use this workflow after checking that the required packages are installed in your environment.

<!-- TODO: confirm whether `odc-stac` is part of the official SDS320 environment. -->

:::

---

## Optional: loading cloud-hosted imagery lazily

For cloud-optimized workflows, you may not need to download a full raw file first. Tools such as `odc-stac`, `stackstac`, `rioxarray` and Dask can create a lazy data object and fetch pixels only when needed.

This pattern is useful but more advanced:

```python
import odc.stac

data = odc.stac.load(
    items,
    bbox=bbox_wgs84,
    crs="EPSG:2056",
    resolution=0.1,
    chunks={"x": 2048, "y": 2048},
)
```

Use this carefully. Lazy loading helps with large data, but it can also make it less obvious when data are actually downloaded.

---

## Python reactivation

Remote sensing data search often uses simple Python objects:

* lists for bounding boxes,
* strings for collection IDs and date ranges,
* dictionaries for query filters,
* loops for inspecting assets.

Example:

```{code-cell} python
query_filter = {"eo:cloud_cover": {"lt": 20}}

bands = ["B02", "B03", "B04", "B08"]

for band in bands:
    print("Requested band:", band)
```

These simple structures make your search reusable. Avoid hard-coding values deep inside long notebooks.

---

## Common pitfalls

| Pitfall                                         | How to avoid it                                    |
| ----------------------------------------------- | -------------------------------------------------- |
| Searching a huge area first                     | Start with a small test bounding box.              |
| Ignoring cloud cover                            | Filter or inspect scenes before using them.        |
| Confusing item and asset                        | Remember: item is the scene; asset is the file.    |
| Downloading bands without knowing their meaning | Inspect asset names and band descriptions first.   |
| Mixing resolutions without noticing             | Check pixel size and resampling choices.           |
| Forgetting CRS differences                      | Track search CRS and output CRS separately.        |
| Treating previews as analysis data              | Use data assets for analysis, not only thumbnails. |

---

## Mini task

Search for remote sensing data for your project area.

You do not need to download anything yet. Produce a short note with:

```text
Catalog or data portal:
Collection or dataset:
Bounding box:
Date range:
Quality filter:
Number of items found:
Most promising item:
Useful assets:
Main limitation:
```

:::{note} Sample solution
:class: dropdown

```text
Catalog or data portal: Microsoft Planetary Computer STAC API
Collection or dataset: sentinel-2-l2a
Bounding box: small WGS84 bounding box around the study area
Date range: summer season of the target year
Quality filter: cloud cover below 20%
Number of items found: to be filled after running search
Most promising item: lowest-cloud item in the date range
Useful assets: B02, B03, B04, B08, SCL
Main limitation: clouds and 10 m spatial resolution may limit small-object mapping
```

:::

---

## Key takeaways

* STAC separates catalogs, collections, items and assets.
* Remote sensing data should be filtered by area, time and quality.
* Inspect metadata and assets before downloading or loading data.
* Start with a small bounding box.
* Record CRS, resolution, bands and limitations as soon as you inspect a dataset.
