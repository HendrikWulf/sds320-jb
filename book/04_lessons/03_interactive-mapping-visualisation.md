---
site:
  outline_maxdepth: 2
---

# L03 – Interactive visualisation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Using interactive maps to inspect data, compare layers and evaluate GeoAI outputs
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

In the previous lesson, you searched for spatial data, inspected candidate sources and created small data subsets. This lesson turns those data into interactive maps.

Interactive mapping is not only a final presentation step. It is part of the whole {term}`GeoAI` workflow. Before modelling, you use maps to inspect imagery, check whether labels align with visible features, compare data sources and identify problems such as clouds, shadows or missing coverage. After {term}`Inference`, you use maps to compare predictions with source imagery, reference labels and spatial context.

This lesson focuses on practical visual checks using Python and {term}`Leafmap`. You will work with raster layers, vector overlays, cloud-hosted datasets, split-panel comparisons and model-style outputs.

The next lessons build on these skills. Data preprocessing, training data creation, segmentation, detection and change detection all depend on your ability to see whether spatial layers actually make sense together.

---

## 2. Motivation

A workflow can run without errors and still be spatially wrong.

A raster may be shifted by one projection issue. A label layer may not match the imagery date. A building mask may look good in one neighbourhood but fail near shadows or tile edges. A model metric may look strong while the map reveals systematic errors.

Interactive maps help you find these problems early because you can zoom, pan, toggle layers and compare datasets at the same location. This is especially important in project work, where you often combine data from different providers, sensors, dates and formats.

A useful rule for SDS320 is:

```text
If a spatial decision affects your project, map it before you trust it.
```

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- create interactive maps with Leafmap in a Jupyter notebook,
- add basemaps, raster layers and vector layers to a map,
- visualise local GeoTIFFs and cloud-hosted COGs,
- use true-colour and false-colour band combinations for imagery inspection,
- preview Planetary Computer items without downloading full datasets,
- compare layers with split-panel maps,
- overlay labels or model outputs on source imagery,
- apply basic visualisation choices that support project communication.

---

## 4. Lesson roadmap

Work through the pages in this order:

1. [Leafmap basics](L03/01_leafmap-basics.md) — create your first interactive maps and use basemaps for spatial context.
2. [Raster data on maps](L03/02_raster-data-on-maps.md) — display local and remote raster data, including GeoTIFFs, COGs and band combinations.
3. [Planetary Computer maps](L03/03_planetary-computer-maps.md) — search, preview and visualise cloud-hosted items before downloading data.
4. [Vector data on maps](L03/04_vector-data-on-maps.md) — overlay GeoJSON files and GeoDataFrames on imagery and style them clearly.
5. [Split-panel comparisons](L03/05_split-panel-comparisons.md) — compare two layers interactively with a draggable divider.
6. [Model results on maps](L03/06_model-results-on-maps.md) — overlay masks, labels or predictions on source imagery for visual evaluation.
7. [Visualisation best practices](L03/07_visualisation-best-practices.md) — choose colormaps, layer names, opacity and context layers deliberately.
8. [Project transfer](L03/08_project-transfer.md) — turn the lesson into a concrete visual inspection plan for your own project.

---

## 5. Project framing

As you work through the lesson, keep your own project in mind.

Use these guiding questions:

- Which datasets do I need to inspect visually before I can trust them?
- Which layers need to be compared: imagery, labels, predictions, reference data or context layers?
- What visual evidence would show that my data are aligned and usable?
- Which map or comparison would help another person understand my project decision?

```{tip}
Do not wait until the final report to make maps. Use maps as quality-control tools while your project is still flexible.
```

---

## 6. Before class

Before class, prepare the following:

- [ ] Review your candidate data sources from Lesson 02.
- [ ] Choose one small {term}`Area of Interest (AOI)` that is suitable for visual inspection.
- [ ] Make sure you can open JupyterLab or VS Code with the SDS320 environment.
- [ ] Bring one dataset or data source you would like to inspect on a map.
- [ ] Note one visual question you need to answer for your project.

<!-- TODO: confirm whether `leafmap`, `geoai`, `rasterio`, `geopandas` and Planetary Computer helper functions are included in the final SDS320 environment. -->

---

## 7. After this lesson

After completing this lesson, you should have:

- at least one interactive map for your project area,
- a first visual check of your main imagery or spatial data,
- a comparison of at least two relevant layers,
- a short note on whether the data look suitable,
- a list of visual problems or uncertainties to address before preprocessing or modelling.

These outputs do not need to be polished. Their purpose is to help you make better project decisions before the workflow becomes more complex.
