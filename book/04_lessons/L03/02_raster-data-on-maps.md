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
This lesson uses four datasets from a Las Vegas building-detection project, hosted publicly on Source Cooperative: {term}`NAIP` aerial imagery (four bands, 60 cm resolution), a LiDAR-derived {abbr}`HAG (Height Above Ground)` raster, building footprint annotations, and a rasterized building {term}`mask <Mask>`. The `geoai.download_file()` function fetches a file only if it is not already present locally.

```{code-cell} python
import geoai

naip_url = "https://data.source.coop/opengeos/geoai/las-vegas-train-naip.tif"
hag_url = "https://data.source.coop/opengeos/geoai/las-vegas-train-hag.tif"

naip_path = geoai.download_file(naip_url)
hag_path = geoai.download_file(hag_url)
```

**2. Add a multi-band raster.**
For imagery like {term}`NAIP`, `add_raster()` automatically composites the first three bands as an {term}`RGB composite <RGB Composite>`.

```{code-cell} python
import leafmap

m = leafmap.Map()
m.add_raster(naip_path, layer_name="NAIP Image")
m
```

**3. Add a single-band raster with a colormap.**
For single-band data such as the HAG raster, specify a {term}`colormap <Colormap>` and a value range with `vmin` and `vmax`. Capping the range keeps outliers from washing out the visual contrast you actually care about.

```{code-cell} python
m.add_raster(
    hag_path,
    vmin=0,
    vmax=10,
    colormap="terrain",
    layer_name="Height Above Ground",
)
m
```

Setting `vmax=10` here caps the display at 10 meters so that building-scale structures stand out clearly against the ground, rather than being compressed by a handful of much taller outliers.

**4. Stream a Cloud-Optimized GeoTIFF directly.**
A {term}`Cloud Optimized GeoTIFF (COG)` can be streamed without downloading the whole file first, which matters once your rasters get large. `add_cog_layer()` handles this directly from a URL.

```{code-cell} python
m2 = leafmap.Map()
m2.add_cog_layer(naip_url, name="Las Vegas NAIP (streamed)")
m2
```

**5. Choose a band combination.**
{term}`Multispectral imagery <Multispectral Imagery>` becomes more informative once you combine bands deliberately. A true-color composite (red, green, blue) looks like a natural photograph. A false-color composite substitutes an infrared band to highlight features invisible to the human eye, such as vegetation vigor. The `indexes` parameter in `add_raster()` controls which bands are used and in what order.

```{code-cell} python
m3 = leafmap.Map()
m3.add_raster(naip_path, indexes=[4, 1, 2], layer_name="False Color")
m3
```

Placing the {abbr}`NIR (Near-Infrared)` band (band 4 in NAIP) in the red channel makes healthy vegetation appear bright red, which makes it easy to separate vegetated areas from impervious surfaces like roads and rooftops.

```{admonition} Matching colormap to data type
:class: tip
Height data reads well with `"terrain"`. Continuous variables with two directions of change, such as temperature anomalies, read better with a diverging colormap like `"coolwarm"`. You will revisit this choice more formally on the [best practices page](07_visualisation-best-practices.md).
```

---

## Python reactivation

`vmin`/`vmax` and `indexes` are ordinary keyword arguments, the same pattern you used constantly in SDS210 with `matplotlib` and `rasterio`. If a raster call is not behaving the way you expect, checking the keyword arguments you passed is usually the fastest place to start.

---

## Common pitfalls

- **Forgetting to set `vmin`/`vmax` on single-band rasters.** Without a sensible range, a handful of extreme values can compress the color scale so much that real variation disappears.
- **Assuming `add_raster()` always shows RGB.** For single-band data, you need to specify a colormap yourself, or the default rendering may not be informative.
- **Mixing up band order.** {term}`NAIP`'s bands are Red (1), Green (2), Blue (3), Near-Infrared (4); an `indexes` list in the wrong order produces a color-shifted image that looks wrong but not obviously broken.

---

## Mini task

Using your own candidate raster from L02 (or the NAIP sample above if you do not have one yet), add it to a map twice: once as a true-color composite and once as a false-color composite with the infrared band in the red channel. Compare what each reveals.

:::{note} Sample solution
:class: dropdown

```{code-cell} python
m = leafmap.Map()
m.add_raster(naip_path, indexes=[1, 2, 3], layer_name="True Color")
m.add_raster(naip_path, indexes=[4, 1, 2], layer_name="False Color")
m
```

Toggling between the two layers in the layer control shows the same area rendered naturally and with vegetation highlighted in red. The false-color view makes it much easier to separate lawns and trees from rooftops and pavement, which is useful groundwork for later lessons on segmentation.
:::

---

## Key takeaways

- `add_raster()` composites multi-band imagery automatically and lets you set a colormap and value range for single-band rasters.
- `add_cog_layer()` streams Cloud-Optimized GeoTIFFs directly from a URL without a full download.
- The `indexes` parameter controls band order and combination; false-color composites reveal information invisible in true color.
- Setting `vmin`/`vmax` deliberately, rather than relying on defaults, is often the difference between a readable and an unreadable single-band raster.
