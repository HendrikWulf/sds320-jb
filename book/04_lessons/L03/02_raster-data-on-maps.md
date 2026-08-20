---
site:
  outline_maxdepth: 2
---

# Raster data on maps

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Displaying local rasters and cloud-hosted imagery interactively
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Displaying a local raster

`leafmap.Map.add_raster()` reads a local raster file (for example a {term}`GeoTIFF`) and adds it to the map as a tile layer, reprojecting and rendering it on the fly:

```python
import leafmap

m = leafmap.Map()
m.add_raster(
    "../L02/data/raw/sentinel2/s2_rome_rgb_subset.tif",
    layer_name="Sentinel-2 Rome subset",
)
m.add_layer_control()
m
```

This reuses the Sentinel-2 subset you already worked with in [L02 – Working with remote sensing data](../L02/02_remote-sensing-data.md), now viewed on an interactive map instead of a static plot.

The `geoai` package provides an equivalent function, `geoai.view_raster()`, which returns a ready-to-display map in one call rather than requiring you to create the `Map` object first:

```python
import geoai

geoai.view_raster(
    "../L02/data/raw/sentinel2/s2_rome_rgb_subset.tif",
    layer_name="Sentinel-2 Rome subset",
)
```

---

## 2. Controlling bands and colour

Both functions accept an `indexes` argument to select specific bands, and a `colormap` argument for single-band rasters:

```python
m.add_raster(
    "../L02/data/raw/landsat/landsat_everest_rgb_subset.tif",
    indexes=[1, 2, 3],       # band order for display
    layer_name="Landsat Everest subset",
)
```

For single-band data such as a classification result or an elevation model, a named Matplotlib colormap (for example `"terrain"` or `"viridis"`) is usually more informative than the default grayscale rendering:

```python
m.add_raster(
    "path/to/single_band_raster.tif",
    colormap="terrain",
    vmin=0,
    vmax=3000,
    layer_name="Elevation",
)
```

Setting `vmin`/`vmax` explicitly avoids the colour scale being stretched by outlier pixels, which can otherwise make an otherwise reasonable raster look washed out.

---

## 3. Cloud Optimized GeoTIFFs

A {term}`Cloud Optimized GeoTIFF (COG)` is structured so that a viewer can request only the pixels needed for the current map view, instead of downloading the whole file. `add_cog_layer()` streams a COG directly from a URL without a local download:

```python
m = leafmap.Map()
m.add_cog_layer(
    "https://example-bucket.s3.amazonaws.com/example_cog.tif",
    name="Remote COG example",
)
```

```{admonition} Not every GeoTIFF is a COG
:class: note
A regular {term}`GeoTIFF` can usually be displayed with `add_raster()` locally, but streaming a large file remotely with `add_cog_layer()` only works well if the file is actually COG-structured. Check the data provider's documentation, or convert the file to COG format with `rasterio` or `gdal_translate` if needed.
```

---

## 4. Key takeaways

* `add_raster()` (leafmap) and `view_raster()` (`geoai`) both display a local raster as a map layer; `view_raster()` is a convenient one-line shortcut.
* `indexes` selects which bands to display; `colormap`, `vmin`, and `vmax` control single-band rendering.
* Cloud Optimized GeoTIFFs can be streamed directly from a URL with `add_cog_layer()`, without downloading the whole file first.
* The next page covers visualising STAC items directly from the Planetary Computer, without downloading anything at all.
