---
site:
  outline_maxdepth: 2
---

# Vector data on maps

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Adding and styling GeoJSON, GeoDataFrames, and point markers on top of raster imagery
</div>
<!-- markdownlint-enable MD033 -->

---

## Why vector visualization matters

Vector data carries the labels, boundaries, and outputs that give raster imagery meaning: building footprints, glacier outlines, detected objects. Overlaying vector features on their source imagery is the standard way to check whether annotations actually line up with what is on the ground, which matters both for your own labels and for anything you download from elsewhere.

---

## Core idea

`leafmap` accepts vector data from files, URLs, or in-memory {term}`GeoDataFrames <GeoDataFrame>`, and gives you direct control over how each feature is styled.

---

## Workflow

### A. Prepare sample data

This page uses the same Willisau building sample as the raster page.

```{code-cell} python
import geoai
import geopandas as gpd
import leafmap

swissimage_url = "https://source.coop/giuz/sds320/L03/data/willisau_2024_swissimage_rgb_subset.tif"
buildings_overture_url = ("https://data.source.coop/giuz/sds320/L03/data/willisau_overture_buildings_subset.geojson")
buildings_OSM_url = ("https://data.source.coop/giuz/sds320/L03/data/willisau_OSM_buildings_subset_epsg4326.geojson")

swissimage_path = geoai.download_file(swissimage_url)
buildings_path = geoai.download_file(buildings_overture_url)
```

### B. Load vector data as a GeoDataFrame

This keeps you in familiar territory from SDS210: read a vector file with `geopandas`, inspect it, then hand it to `leafmap`.

```{code-cell} python
gdf = gpd.read_file(buildings_path)

print(f"Features: {len(gdf)}")
gdf.head()
```

Before mapping, check whether the layer has a CRS and expected geometry type.

```{code-cell} python
print("CRS:", gdf.crs)
print("Geometry types:")
print(gdf.geom_type.value_counts())
```

### C. Overlay it on the source imagery

Adding vector labels directly on top of the raster they came from is the fastest way to catch a misaligned or incomplete annotation.

```{code-cell} python
m = leafmap.Map()
m.add_raster(swissimage_path, layer_name="SWISSIMAGE")
m.add_gdf(gdf, layer_name="Building Footprints", zoom_to_layer=True)
m
```

### D. Add vector data directly from a URL

You do not always need to load a file into a GeoDataFrame first; `add_geojson()` works directly from a URL, if the dataset is projected in geographic coordinates `EPSG:4326`.

```{code-cell} python
m2 = leafmap.Map()
m2.add_raster(swissimage_path, zoom_to_layer=True)
m2.add_geojson(buildings_OSM_url, layer_name="Buildings", zoom_to_layer=True)
m2
```

### E. Style vector layers deliberately

A style dictionary follows the Leaflet path-options convention: `color` (outline), `weight` (outline width), `fillColor`, and `fillOpacity`.

```{code-cell} python
style = {
    "color": "red",
    "weight": 2,
    "fillColor": "yellow",
    "fillOpacity": 0.3,
}

m3 = leafmap.Map()
m3.add_raster(swissimage_path)
m3.add_gdf(gdf, layer_name="Styled Buildings", style=style, zoom_to_layer=True)
m3
```

### F. Add point markers

For point data, such as sample locations or points of interest, use `add_marker()`. For datasets with many points, consider marker clustering so the map stays readable at broader zoom levels.

```{code-cell} python
m4 = leafmap.Map(center=[47.11677, 7.99028], zoom=11)
m4.add_marker(location=[47.11677, 7.99028])     # Kantonsschule Willisau
m4
```

```{admonition} Overlay before you trust
:class: important
If you are working with labels you did not create yourself, whether downloaded or provided, overlay them on the source imagery before using them for anything else. Misaligned labels are far easier to spot visually than they are to detect from summary statistics.
```

---

## Python reactivation

The `style` dictionary here is an ordinary Python dictionary with string keys, the same structure you used for {term}`project parameters <Project Parameter>` in SDS210. If a style is not applying, check for a typo in a key name (`fillColor`, not `fillcolor`) before assuming something is broken with the map itself.

---

## Common pitfalls

- **Assuming default styling is good enough for a final figure.** The default outline and fill are fine for quick checks, but a final report figure usually needs deliberate color and opacity choices.
- **Forgetting `zoom_to_layer=True`.** Without it, the map may stay at its previous view and your new vector layer can be off-screen.
- **Overlaying too many points without clustering.** A dense point layer without clustering becomes unreadable at low zoom levels.

---

## Mini task

Load a vector dataset relevant to your project (or the Willisau buildings example above), overlay it on its source imagery, and apply a custom style that makes the features stand out clearly against the basemap.

:::{note} Sample solution
:class: dropdown

```{code-cell} python
style = {
    "color": "white", 
    "weight": 2, 
    "fillColor": "white", 
    "fillOpacity": 0.15
}

m = leafmap.Map()
m.add_raster(swissimage_path)
m.add_gdf(gdf, layer_name="Buildings", style=style, zoom_to_layer=True)
m
```

A thin, high-contrast outline with a low fill opacity keeps individual building shapes visible while still letting the underlying imagery show through, which is often more useful for visual quality control than a solid fill.
:::

---

## Key takeaways

- `add_gdf()`, `add_geojson()`, and `add_marker()` cover the common ways of putting vector data on a `leafmap` map.
- A style dictionary controls outline color, outline width, fill color, and fill opacity.
- Overlaying vector labels on their source imagery is the standard way to check annotation quality before relying on them.
- Point layers with many features benefit from clustering to stay readable at broader zoom levels.
