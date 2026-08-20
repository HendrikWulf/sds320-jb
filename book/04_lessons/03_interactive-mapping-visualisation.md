# L03 – Interactive mapping

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning rasters, vectors, and model outputs into maps you and others can explore
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

You now know what {term}`GeoAI` is, and in [L02](02_data-acquisition.md) you learned how to find and access raster and vector data. This lesson is about seeing that data: building interactive maps in a Jupyter notebook that let you and your audience explore imagery, vector layers, and — later in the course — model predictions.

Interactive maps are not only a presentation tool. Panning, zooming, and toggling layers is often the fastest way to spot a misaligned dataset, a suspicious gap, or a promising study area, well before you write any analysis code.

---

## 2. Motivation

A static screenshot can only show one view of your data at one moment. An interactive map lets you and your reader check coverage, zoom into details, compare two time periods, or toggle a model prediction against the underlying imagery — all without re-running code. This is especially valuable in a GeoAI workflow, where a "wrong-looking" prediction is often easiest to diagnose by looking at it directly on a map next to the input imagery.

This lesson focuses on `leafmap`, the interactive mapping package used throughout this book, and on the mapping helpers built into the `geoai` package for common GeoAI-specific tasks such as showing STAC items or comparing predictions.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

* create a basic interactive map with `leafmap` and add a basemap,
* display a local raster or {term}`Cloud Optimized GeoTIFF (COG)` on a map,
* visualise a {term}`SpatioTemporal Asset Catalog (STAC)` item directly from the {term}`Planetary Computer` without downloading it first,
* add vector layers to a map with styling and popups,
* build a split-panel map to compare two layers side by side,
* preview model prediction outputs on a map, in anticipation of the modelling lessons later in this book,
* apply basic best practices for choosing between interactive and static maps.

---

## 4. Lesson roadmap

Work through the pages in this order:

1. [Leafmap basics](L03/01_leafmap-basics.md) — create your first interactive map and add a basemap.
2. [Raster data on maps](L03/02_raster-data-on-maps.md) — display local rasters and COGs, with band and colormap control.
3. [Planetary Computer maps](L03/03_planetary-computer-maps.md) — visualise STAC items directly from a cloud catalogue.
4. [Vector data on maps](L03/04_vector-data-on-maps.md) — add and style vector layers, with popups and legends.
5. [Split-panel comparisons](L03/05_split-panel-comparisons.md) — compare two layers side by side in one map.
6. [Model results on maps](L03/06_model-results-on-maps.md) — preview how prediction outputs will be visualised in later lessons.
7. [Visualisation best practices](L03/07_visualisation-best-practices.md) — choosing colormaps, basemaps, and interactive versus static output.
8. [Project transfer](L03/08_project-transfer.md) — build a first interactive map for your own project data.

---

## 5. Project framing

As you work through this lesson, try building maps with your own project's data where possible — the raster and vector sources you identified in [L02](02_data-acquisition.md) are a good starting point.

* Which layer from my project would benefit most from an interactive view rather than a static plot?
* Do I need to compare two datasets, two dates, or a prediction against ground truth?
* Who is the audience for this map — myself while exploring, or a reader in my final report?

```{tip}
An interactive map you build now for exploration does not need to be polished. Save the cartographic polish — legends, titles, colour choices — for the maps that go into your final report, covered in the [Project handbook's figures and maps page](../03_project/08_figures-and-maps.md).
```

---

## 6. Before class

Before class, prepare the following:

* [ ] Make sure `leafmap` and `geoai` run in your notebook environment (see [Python & project setup](../02_setup.md) if not).
* [ ] Have at least one local raster file or vector file from [L02](02_data-acquisition.md) ready to visualise.
* [ ] Think of one comparison you would like to see on a map for your project (two dates, two sources, or prediction versus truth).

---

## 7. After this lesson

After completing this lesson, you should have:

* a working interactive map built with `leafmap`,
* experience displaying at least one raster and one vector layer on a map,
* a split-panel comparison of two layers,
* a sense of how model outputs will be visualised once you reach the modelling lessons,
* a short list of visualisation choices to revisit when you prepare figures for your final report.
