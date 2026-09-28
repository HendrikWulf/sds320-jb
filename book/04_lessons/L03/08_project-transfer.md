---
site:
  outline_maxdepth: 1
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning this lesson into progress on your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Intro

You now have a working set of visualization techniques: basemaps, raster and vector overlays, cloud-hosted previews, split-panel comparisons, and result overlays. The goal of this page is not new theory. It is to turn those techniques into a short, reusable notebook you will come back to throughout the rest of the semester, every time you acquire new data, generate training tiles, or produce a prediction.

---

## 2. Project checklist

- [ ] Loaded your own candidate dataset (or datasets) from L02 onto an interactive map
- [ ] Checked visually whether any labels or annotations you have align with the source imagery
- [ ] Chosen a band combination or colormap that best reveals the pattern your project cares about
- [ ] Built at least one split-panel comparison relevant to your project (two dates, two sources, or labels vs. imagery)
- [ ] Applied the five-point best-practices self-check from the [previous page](07_visualisation-best-practices.md) to at least one map

---

## 3. Decision points

**Which basemap fits your project by default?** If your project depends on visually verifying structures or land cover, a satellite basemap is usually the right default. If it depends on terrain or hydrology, a topographic basemap may serve you better.

**Which band combination or colormap best reveals your target?** If you are not sure yet, try at least two options side by side, as in the mini task on the [raster data page](02_raster-data-on-maps.md), and pick based on what you actually see rather than habit.

**What will you need to compare later, and can you set that comparison up now?** If your project involves change detection, before/after imagery, or model evaluation, a split-panel pattern you build now will still be useful once you have real predictions.

---

## 4. Common pitfalls

- **Building one-off maps you cannot reuse.** If you find yourself rewriting the same `leafmap.Map()` setup repeatedly, turn it into a small function instead. This will save time from L04 onward.
- **Never checking label alignment before relying on labels.** If your project uses existing annotations (from OpenStreetMap, a government dataset, or elsewhere), verify them visually before treating them as ground truth.
- **Overloading a single map with every layer you have.** A map with too many simultaneous layers becomes as hard to read as no map at all. Split panels or separate maps are often clearer than one crowded view.

---

## 5. Mini deliverable

Produce a short **Project Visualization Notebook** containing:

1. An interactive map of your project's study area with an appropriate basemap.
2. At least one raster or vector layer from your own candidate data, styled deliberately (not left at default settings).
3. One split-panel comparison relevant to your project question.
4. A two- or three-sentence note on what the visualization revealed, including anything that looked wrong or unexpected.

Keep this notebook. You will extend it directly in [L04 – Training data](../04_training-data.md) and again once you train a classifier in [L05 – Image recognition](../05_image-recognition.md).

---

## 6. Reflection questions

- Did anything about your data look different once you actually visualized it, compared to what you expected from L02?
- If you have candidate labels, did they align cleanly with your imagery, or did you spot a misalignment worth investigating further?
- Which visualization technique from this lesson do you expect to use most often for your specific project, and why?
- If you had to show one map from this lesson to someone unfamiliar with your project, which one would you choose, and what would you need to add (a legend, a caption, a basemap) to make it understandable to them?

---

## 7. Companion notebooks

The examples in this lesson are intentionally kept short so that you can focus on the main visualization ideas. If you want to revisit the complete workflows, or understand how the example datasets used in L03 were prepared, you can find the supporting notebooks in the SDS320 folder on [Source Cooperative](https://data.source.coop/giuz/sds320/L03/notebooks/).

The four notebooks serve **two different purposes**:

- **Learning notebooks** extend the lesson and are useful for practising, exploring, and adapting the workflows to your own project.
- **Reproducibility notebooks** document how some of the prepared datasets used in the lesson were downloaded and created. You do **not** need to rerun these notebooks to complete L03, but they let you trace the example data back to their source and reproduce the preparation workflow yourself.

### Learning notebooks

**[Leafmap recap notebook](https://data.source.coop/giuz/sds320/L03/notebooks/SDS320_L03_learning_leafmap_recap.ipynb)**  
This notebook combines the main `leafmap` techniques from L03 into one start-to-finish workflow: basemaps, raster and vector layers, band combinations, split-panel comparisons, model-result overlays, Planetary Computer previews, and project transfer. Use it after the session if you want to **recapitulate the complete visualization workflow in one place**. A useful exercise is to run it from top to bottom first and then replace the sample datasets with your own project data.

**[SWISSIMAGE, swissBUILDINGS3D and Overture Buildings walkthrough](https://data.source.coop/giuz/sds320/L03/notebooks/SDS320_L03_learning_SWISSIMAGE_swissBUILDINGS3D_download_viz.ipynb)**  
This notebook provides a more detailed example of how **data acquisition and visualization connect**. Using a small area around UZH Campus Irchel, it moves from defining an area of interest through a swisstopo STAC search, spatial subsetting, CRS handling, and data inspection to an interactive comparison of SWISSIMAGE, swissBUILDINGS3D, and Overture building footprints. Use it when you want to understand the steps between **finding a dataset and deciding whether it is suitable for your project**.

### Reproducibility notebooks

**[Reproduce the Sentinel-2 subset](https://data.source.coop/giuz/sds320/L03/notebooks/SDS320_L03_reproduce_S2_subset_download.ipynb)**  
This notebook documents how the Sentinel-2 example for Willisau was prepared. It queries the Microsoft Planetary Computer, selects a low-cloud Sentinel-2 scene, reads the required spectral bands, resamples them to a common grid, saves the spatial subset as a multiband GeoTIFF, and creates a quick visual preview. Consult it if you want to understand **where the Sentinel-2 file used in the lesson came from** or reproduce a similar subset for another study area.

**[Reproduce the Swiss geodata subsets](https://data.source.coop/giuz/sds320/L03/notebooks/SDS320_L03_reproduce_swiss_geodata_download_v1.ipynb)**  
This notebook documents how several of the Willisau datasets used in L03 were assembled. It searches the swisstopo STAC catalogue, mosaics and crops SWISSIMAGE, SwissSurface3D and SwissALTI3D data, derives a **Height Above Ground (nDSM)** raster, and retrieves building data from swissBUILDINGS3D, Overture Maps, and OpenStreetMap. Use it as a reference when you want to reproduce the lesson data or build a similar **download → subset → derive → inspect** pipeline for your own project.

```{tip}
Start with the **learning notebooks** if your goal is to practise the methods from L03. Open the **reproducibility notebooks** when you need to understand data provenance, repeat the preparation of the lesson datasets, or adapt the download workflow to a new study area.
```
