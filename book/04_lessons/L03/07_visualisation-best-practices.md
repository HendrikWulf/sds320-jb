---
site:
  outline_maxdepth: 2
---

# Visualisation best practices

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Choosing colormaps, basemaps, and when to go static instead of interactive
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Scope of this page

This page covers practical choices specific to building *interactive* maps in a notebook. For the broader question of preparing polished, publication-ready figures and maps for your final report, see the [Project handbook's figures and maps page](../../03_project/08_figures-and-maps.md) — the two pages are complementary, not duplicates.

---

## 2. Interactive or static?

Not every visual belongs in an interactive map. A useful rule of thumb:

| Use an interactive map when… | Use a static plot when… |
| --- | --- |
| You want to explore coverage or spot-check data quality | You need a figure for a written report |
| The audience will view it in a notebook or a web page | The audience will view it on paper or in a slide |
| You want to compare layers by toggling or sliding | You need precise control over legend, scale, and annotation |
| The data is too large to embed as a static image | The exact visual needs to be reproducible byte-for-byte |

```{tip}
It is common to use an interactive map during exploration, and then export a specific, well-chosen view as a static figure once you know what you want to show.
```

---

## 3. Choosing a colormap

* For continuous, one-directional data (elevation, distance, count), use a sequential colormap such as `"viridis"` or `"YlGnBu"`.
* For data with a meaningful midpoint (change, anomaly, difference from a reference), use a diverging colormap such as `"RdYlGn"` or `"RdBu"`, centred on that midpoint via `vmin`/`vmax`.
* Avoid the classic rainbow colormap (`"jet"`) for continuous data — it has no consistent perceptual ordering and can visually exaggerate boundaries that are not actually there.
* For categorical data (land cover classes, suitability classes), use a small set of clearly distinguishable, colorblind-safe colours rather than a continuous colormap.

---

## 4. Choosing a basemap

A basemap should support your data, not compete with it. A busy street basemap under a semi-transparent raster can make both illegible; a plain terrain or light basemap usually works better as visual context. If your data already provides full visual context (a true-colour satellite composite, for example), consider turning the basemap off, or using a minimal one only for orientation at low zoom levels.

---

## 5. Performance considerations

Large rasters and vector layers with many thousands of features can make an interactive map slow to render and pan. A few practical mitigations:

* Prefer {term}`Cloud Optimized GeoTIFF (COG)` sources with `add_cog_layer()` over embedding a large local raster directly — tiles are only requested for the current view.
* Simplify very detailed vector geometries before adding them to a map, if geometric precision is not needed for the visual.
* Clip a large vector layer to your area of interest before adding it, rather than the full dataset (the `bbox` argument of `add_vector()`, introduced on the [vector data page](04_vector-data-on-maps.md), helps here).

---

## 6. Key takeaways

* Choose interactive maps for exploration and static figures for your final report — see the [figures and maps page](../../03_project/08_figures-and-maps.md) for that side of the decision.
* Match the colormap to the data type: sequential for continuous, diverging for data centred on zero, categorical for classes — and avoid `"jet"`.
* Let the basemap support your data rather than compete with it.
* Use COGs, geometry simplification, and bounding-box filtering to keep large interactive maps responsive.
