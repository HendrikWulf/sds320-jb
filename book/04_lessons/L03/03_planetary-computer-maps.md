---
site:
  outline_maxdepth: 2
---

# Planetary Computer maps

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Previewing cloud-hosted geospatial data before downloading it
</div>
<!-- markdownlint-enable MD033 -->

---

## Why this page matters

In the previous page, you displayed local and remote raster layers. Here, you use interactive maps to preview data from the Microsoft {term}`Planetary Computer`.

This connects directly to Lesson 02. During data acquisition, you searched for candidate scenes and assets. In this page, you inspect them visually before deciding whether they are useful enough to download or process.

The next page moves from raster previews to vector overlays.

---

## Why this workflow matters

Downloading a full imagery archive before you know whether it is even useful wastes time and disk space. Being able to search, preview, and inspect metadata first, and only download what you actually need, is a core skill for the data-acquisition side of your project, and it connects directly back to [L02 – Data acquisition](../02_data-acquisition.md).

---

## Core idea

Microsoft {term}`Planetary Computer` hosts a large volume of geospatial data through a {term}`SpatioTemporal Asset Catalog (STAC)` API. The `geoai` package wraps that API so you can search, visualize, and selectively download data without leaving Python.

---

## Workflow

### A. Browse available collections

`pc_collection_list()` returns every available {term}`STAC collection <STAC Collection>` as a table, which is a useful way to see what exists before writing a specific search.

```{code-cell} python
import geoai

collections = geoai.pc_collection_list()
print(f"Total collections: {len(collections)}")
collections.head(10)
```

Scanning the full table gets unwieldy once you know roughly what you want. Pass `filter_by` with a keyword to match against the collection `id` and narrow the results down to the collection you are after:

```{code-cell} python
collections = geoai.pc_collection_list(filter_by={"id": "sentinel-3"})
print(f"Total collections: {len(collections)}")
collections.head(10)
```

### B. Search for items matching your area and time range

`pc_stac_search()` takes a collection ID, a bounding box in `[west, south, east, north]` format, and a time range. Each result is a {term}`STAC item <STAC Item>` with metadata and links to the underlying data.

```{code-cell} python
landsat_items = geoai.pc_stac_search(
    collection="landsat-c2-l2",
    bbox=[121.4919, -30.7738, 121.5081, -30.7662], #  Western Australia, Kalgoorlie Super Pit
    time_range="2024-09-01/2024-11-30",
    query={"eo:cloud_cover": {"lt": 10}},
    max_items=10,
)
landsat_items
```

A single `time_range` only covers one continuous interval, so it cannot express "the same Sep–Nov window, repeated across several years" in one call. Loop over the years instead and concatenate the results into a single list:

```{code-cell} python
years = [2021, 2022, 2023, 2024]
landsat_items = []
for y in years:
    landsat_items += geoai.pc_stac_search(
        collection="landsat-c2-l2",
        bbox=[121.4919, -30.7738, 121.5081, -30.7662],
        time_range=f"{y}-09-01/{y}-11-30",
        query={"eo:cloud_cover": {"lt": 10}},
        max_items=10,
    )
```

### C. Check what data is actually attached to an item

A STAC item bundles several {term}`STAC assets <STAC Asset>`, image bands, metadata, and more, and asset keys differ between collections, so it is worth checking before you write code that assumes a particular key exists.

```{code-cell} python
geoai.pc_item_asset_list(landsat_items[0])
```

### D. Preview an item without downloading it

`view_pc_item()` streams tiles from Planetary Computer's tile server directly onto a `leafmap` map, so you can inspect a search result before committing to a download.

```{code-cell} python
geoai.view_pc_item(
    item=landsat_items[0], 
    assets=["swir22", "nir08", "red"],
    backend="ipyleaflet",
)
```

### E. Compute a spectral index on the fly

For multispectral collections like Landsat, you can pass an `expression` to compute a {term}`spectral index <Spectral Index>` server-side, without downloading anything. Here is the {abbr}`NDVI (Normalized Difference Vegetation Index)`, a common measure of vegetation greenness:

```{code-cell} python
geoai.view_pc_item(
    item=landsat_items[0],
    expression="(nir08-red)/(nir08+red)",
    rescale="-0.5,0.5",
    colormap_name="rdylgn",
    name="NDVI",
    backend="ipyleaflet",
)
```

### F. Download only what you need

Once you have confirmed an item is useful, download specific assets rather than the whole collection.

```python
geoai.pc_stac_download(
    landsat_items[0], 
    output_dir="../data/raw/Landsat", 
    assets=["nir08", "red"]
)
```

`pc_stac_download()` has no bounding-box or clipping option: it streams whichever assets you list straight to disk at full extent. The `bbox` you passed to `pc_stac_search()` only filtered which items matched spatially, it has no effect on what gets downloaded.

To actually restrict the download to a bounding box, read the (cloud-optimized) asset with rioxarray and clip it before writing to disk — this uses COG range requests, so it only pulls the bytes covering your bbox rather than the whole scene:

```python
import os
import planetary_computer as pc
import rioxarray as rxr

bbox=[121.4919, -30.7738, 121.5081, -30.7662]  # west, south, east, north

def download_clipped(items, assets, bbox, output_dir="../data/raw/Landsat"):
    os.makedirs(output_dir, exist_ok=True)
    paths = {}
    for item in items:
        signed = pc.sign(item)
        for asset_key in assets:
            if asset_key not in signed.assets:
                continue
            url = signed.assets[asset_key].href
            da = rxr.open_rasterio(url, masked=True)
            clipped = da.rio.clip_box(*bbox, crs="EPSG:4326")
            out_path = os.path.join(output_dir, f"{item.id}_{asset_key}_clip.tif")
            clipped.rio.to_raster(out_path, driver="COG")
            paths.setdefault(item.id, {})[asset_key] = out_path
    return paths

clipped_paths = download_clipped(landsat_items, ["nir08", "red"], bbox)
```

```{admonition} Preview before you commit
:class: tip
Every step above except the last one avoids downloading full-resolution data. Get in the habit of previewing with `view_pc_item()` before you call a download function, especially for large collections.
```

### G. Apply the same steps to other collections

The search → check assets → preview pattern from steps B–D works the same way for any collection in the catalog. The two dropdowns below work through it for NAIP aerial imagery and for a land-cover classification collection.

:::{note} NAIP imagery
:class: dropdown

{term}`NAIP` provides high-resolution aerial photography for the United States, useful when a project needs finer spatial detail than satellite collections like Landsat or Sentinel-2 can offer.

```{code-cell} python
naip_items = geoai.pc_stac_search(
    collection="naip",
    bbox=[-119.2974, 34.4172, -119.2081, 34.4674], # Ojai, California
    time_range="2013-01-01/2014-12-31",
)
naip_items
```

```{code-cell} python
geoai.pc_item_asset_list(naip_items[0])
```

NAIP items expose a single combined `"image"` asset rather than separate spectral bands, since the imagery is delivered pre-composited:

```{code-cell} python
geoai.view_pc_item(
    item=naip_items[1], 
    assets=["image"],
    backend="ipyleaflet",
    map_args={"zoom": 16}, # if you don't see the image on the basemap, zoom further in
)
```

:::

:::{note} Land cover data
:class: dropdown

Collections such as `io-lulc-annual-v02` provide annual, global {term}`land-cover classification <Land Cover Classification>` products, categorical maps of classes such as water, trees, crops, or built-up area, rather than continuous spectral imagery:

```{code-cell} python
lc_items = geoai.pc_stac_search(
    collection="io-lulc-annual-v02",
    bbox=[-74.8500, 10.9300, -74.7500, 11.0200],  # Barranquilla, Colombia
    time_range="2021-01-01/2022-12-31",
    max_items=10,
)
lc_items
```

Because the classes are categorical, a qualitative colormap such as `"tab10_r"` fits better than the continuous colormaps used for spectral indices, matching the categorical-colormap guidance under Common pitfalls:

```{code-cell} python
m = geoai.view_pc_item(
    item=lc_items[0],
    colormap_name="tab10_r",
    basemap="SATELLITE",
    backend="ipyleaflet",
)
m
```

:::

---

## Python reactivation

Search results behave like a list of objects (`naip_items[0]`), and filters are passed as dictionaries (`query={"eo:cloud_cover": {"lt": 1}}`), the same nested-dictionary pattern you used for JSON-like structures in SDS210. If a search returns nothing, check your bounding box and time range before assuming the collection has no data.

---

## Common pitfalls

- **Downloading before previewing.** Large collections can be expensive to pull down; check with `view_pc_item()` first.
- **Using a sequential colormap for categorical data.** Land cover classes need a qualitative colormap like `"tab10"`, not a continuous one like `"viridis"`.
- **Assuming every collection has the same asset keys.** Always check `pc_item_asset_list()` for a new collection rather than guessing.

---

## Mini task

Search a Planetary Computer collection using the rough bounding box of your own project's study area (or the Baltimore example above if you do not have one yet). List the available assets for the first result, and preview it on a map.

:::{note} Sample solution
:class: dropdown

```{code-cell} python
items = geoai.pc_stac_search(
    collection="naip",
    bbox=[-76.6657, 39.2648, -76.6478, 39.2724],
    time_range="2018-01-01/2020-12-31",
)
print(geoai.pc_item_asset_list(items[0]))
geoai.view_pc_item(item=items[0])
```

If the search returns no items, the most likely causes are a bounding box outside the collection's coverage area or a time range with no acquisitions, both worth checking before assuming the data does not exist.
:::

---

## Further reading

- [Microsoft Planetary Computer catalog](https://planetarycomputer.microsoft.com)

---

## Key takeaways

- `geoai`'s Planetary Computer functions let you search, preview, and selectively download cloud-hosted data through a {term}`STAC <SpatioTemporal Asset Catalog (STAC)>` API.
- `view_pc_item()` streams tiles for preview without requiring a download.
- Categorical data needs a qualitative colormap; continuous data needs a sequential or diverging one.
- Band math expressions such as NDVI can be computed server-side and previewed before you download anything.
