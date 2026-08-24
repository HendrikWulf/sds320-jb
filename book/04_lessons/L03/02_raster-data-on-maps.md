---
site:
  outline_maxdepth: 2
---

# Raster data on maps

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Displaying local and cloud-hosted rasters with appropriate colormaps
</div>
<!-- markdownlint-enable MD033 -->

---

## Why raster visualization matters

Raster data, satellite imagery, elevation models, model outputs, is the backbone of most GeoAI workflows. Before you can trust a raster in an analysis, you need to see it: does it cover the area you expect, does it have the bands you think it has, and does anything look obviously wrong?

---

## Core idea

`leafmap` renders rasters as map tile layers. For multi-band imagery it can automatically composite an RGB view; for single-band rasters, you choose a {term}`colormap` and a value range yourself.

---

## Workflow

**1. Get some sample data.**
This lesson uses four datasets from a Las Vegas building-detection project, hosted publicly on Source Cooperative: {term}`swissimage` aerial imagery (four bands, 60 cm resolution), a LiDAR-derived {abbr}`HAG (Height Above Ground)` raster, building footprint annotations, and a rasterized building {term}`mask <Mask>`. The `geoai.download_file()` function fetches a file only if it is not already present locally.

```{code-cell} python
import geoai

swissimage_url = "https://source.coop/giuz/sds320/L03/data/willisau_2024_swissimage_rgb_subset.tif"
hag_url = "https://source.coop/giuz/sds320/L03/data/willisau_height_above_ground_ndsm.tif”
s2_url = "https://source.coop/giuz/sds320/L03/data/willisau_2026-07-24_sentinel2_subset.tif"

swissimage_path = geoai.download_file(swissimage_url)
hag_path = geoai.download_file(hag_url)
s2_path = geoai.download_file(s2_url)
```

**2. Add a multi-band raster.**
For imagery like {term}`swissimage`, `add_raster()` automatically composites the first three bands as an {term}`RGB composite <RGB Composite>`.

```{code-cell} python
import leafmap

m = leafmap.Map()
m.add_raster(swissimage_path, layer_name="swissimage")
m
```

**3. Add a single-band raster with a colormap.**
For single-band data such as the HAG raster, specify a {term}`colormap <Colormap>` and a value range with `vmin` and `vmax`. Capping the range keeps outliers from washing out the visual contrast you actually care about.

```{code-cell} python
m.add_raster(
    hag_path,
    vmin=0,
    vmax=10,
    colormap="plasma",
    layer_name="Height Above Ground",
)
m
```

Setting `vmax=10` here caps the display at 10 meters so that building-scale structures stand out clearly against the ground, rather than being compressed by a handful of much taller outliers.

**4. Stream a Cloud-Optimized GeoTIFF directly.**
A {term}`Cloud Optimized GeoTIFF (COG)` can be streamed without downloading the whole file first, which matters once your rasters get large. `add_cog_layer()` handles this directly from a URL.

```{code-cell} python
m2 = leafmap.Map()
m2.add_cog_layer(swissimage_url, name="Willisau swissimage (streamed)")
m2
```

**5. Choose a band combination.**
{term}`Multispectral imagery <Multispectral Imagery>` becomes more informative once you combine bands deliberately. A true-color composite (red, green, blue) looks like a natural photograph. A false-color composite substitutes an infrared band to highlight features invisible to the human eye, such as vegetation vigor. The `indexes` parameter in `add_raster()` controls which bands are used and in what order.

```{code-cell} python
m3 = leafmap.Map()
m3.add_raster(s2_path, indexes=[5, 4, 3], layer_name="False Color")
m3
```

Placing the {abbr}`NIR (Near-Infrared)` band (band 4 in this Sentinel-2 subset) in the green channel makes healthy vegetation appear bright green, which makes it easy to separate vegetated areas from impervious surfaces like roads and rooftops.

```{admonition} Matching colormap to data type
:class: tip
Continous height data reads well with a sequential colormap like `"viridis"`, which is perceptually uniform. Continuous variables with two directions of change, such as temperature anomalies, read better with a diverging colormap like `"coolwarm"`. You will revisit this choice more formally on the [best practices page](07_visualisation-best-practices.md).
```

---

## Python reactivation

`vmin`/`vmax` and `indexes` are ordinary keyword arguments, the same pattern you used constantly in SDS210 with `matplotlib` and `rasterio`. If a raster call is not behaving the way you expect, checking the keyword arguments you passed is usually the fastest place to start.

---

## Common pitfalls

- **Forgetting to set `vmin`/`vmax` on single-band rasters.** Without a sensible range, a handful of extreme values can compress the color scale so much that real variation disappears.
- **Assuming `add_raster()` always shows RGB.** For single-band data, you need to specify a colormap yourself, or the default rendering may not be informative.
- **Mixing up band order.** Sentinel-2 data has 13 spectral bands ranging from the visible to the Shortwave-Infrared spectrum of which we sampled 6 bands; an `indexes` list in the wrong order can cause confusion.

---

## Mini task

Using your own candidate raster from L02 (or the Sentinel-2 sample above if you do not have one yet), add it to a map twice: once as a true-color composite and once as a false-color composite with the infrared band in the red channel. Compare what each reveals.

:::{note} Sample solution
:class: dropdown

```{code-cell} python
m = leafmap.Map()
m.add_raster(s2_path, indexes=[1, 2, 3], layer_name="True Color")
m.add_raster(s2_path, indexes=[4, 3, 2], layer_name="False Color 1")
m.add_raster(s2_path, indexes=[6, 4, 3], layer_name="False Color 2")
m
```

Toggling between the three layers in the layer control shows the same area rendered naturally and with vegetation highlighted in red and green. The false-color views makes it much easier to separate vegetation from urban spaces, which is useful groundwork for later lessons on segmentation.
:::

---

## Key takeaways

- `add_raster()` composites multi-band imagery automatically and lets you set a colormap and value range for single-band rasters.
- `add_cog_layer()` streams Cloud-Optimized GeoTIFFs directly from a URL without a full download.
- The `indexes` parameter controls band order and combination; false-color composites reveal information invisible in true color.
- Setting `vmin`/`vmax` deliberately, rather than relying on defaults, is often the difference between a readable and an unreadable single-band raster.
