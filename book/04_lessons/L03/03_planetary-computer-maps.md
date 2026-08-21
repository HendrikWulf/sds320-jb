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

**1. Browse available collections.**
`pc_collection_list()` returns every available {term}`STAC collection <STAC Collection>` as a table, which is a useful way to see what exists before writing a specific search.

```{code-cell} python
import geoai

collections = geoai.pc_collection_list()
print(f"Total collections: {len(collections)}")
collections.head(10)
```

**2. Search for items matching your area and time range.**
`pc_stac_search()` takes a collection ID, a bounding box in `[west, south, east, north]` format, and a time range. Each result is a {term}`STAC item <STAC Item>` with metadata and links to the underlying data.

```{code-cell} python
naip_items = geoai.pc_stac_search(
    collection="naip",
    bbox=[-76.6657, 39.2648, -76.6478, 39.2724],
    time_range="2013-01-01/2014-12-31",
)
naip_items
```

**3. Check what data is actually attached to an item.**
A STAC item bundles several {term}`STAC assets <STAC Asset>`, image, metadata, thumbnail, and more, and asset keys differ between collections, so it is worth checking before you write code that assumes a particular key exists.

```{code-cell} python
geoai.pc_item_asset_list(naip_items[0])
```

**4. Preview an item without downloading it.**
`view_pc_item()` streams tiles from Planetary Computer's tile server directly onto a `leafmap` map, so you can inspect a search result before committing to a download.

```{code-cell} python
geoai.view_pc_item(item=naip_items[0])
```

**5. Preview categorical data with the right colormap.**
Derived products such as land cover maps are categorical rather than continuous, so they need a qualitative colormap rather than a sequential one.

```{code-cell} python
lc_items = geoai.pc_stac_search(
    collection="chesapeake-lc-13",
    bbox=[-76.6657, 39.2648, -76.6478, 39.2724],
    time_range="2013-01-01/2014-12-31",
    max_items=10,
)
geoai.view_pc_item(item=lc_items[0], colormap_name="tab10", basemap="SATELLITE")
```

**6. Compute a spectral index on the fly.**
For multispectral collections like Landsat, you can pass an `expression` to compute a {term}`spectral index <Spectral Index>` server-side, without downloading anything. Here is the {abbr}`NDVI (Normalized Difference Vegetation Index)`, a common measure of vegetation greenness:

```{code-cell} python
landsat_items = geoai.pc_stac_search(
    collection="landsat-c2-l2",
    bbox=[-76.6657, 39.2648, -76.6478, 39.2724],
    time_range="2024-10-27/2024-12-31",
    query={"eo:cloud_cover": {"lt": 1}},
    max_items=10,
)

geoai.view_pc_item(
    item=landsat_items[0],
    expression="(nir08-red)/(nir08+red)",
    rescale="-1,1",
    colormap_name="greens",
    name="NDVI",
)
```

**7. Download only what you need.**
Once you have confirmed an item is useful, download specific assets rather than the whole collection.

```{code-cell} python
geoai.pc_stac_download(naip_items, output_dir="data", assets=["image", "thumbnail"])
```

```{admonition} Preview before you commit
:class: tip
Every step above except the last one avoids downloading full-resolution data. Get in the habit of previewing with `view_pc_item()` before you call a download function, especially for large collections.
```

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