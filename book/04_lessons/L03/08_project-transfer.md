---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning this lesson into progress on your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## Intro

You now have a working set of visualization techniques: basemaps, raster and vector overlays, cloud-hosted previews, split-panel comparisons, and result overlays. The goal of this page is not new theory. It is to turn those techniques into a short, reusable notebook you will come back to throughout the rest of the semester, every time you acquire new data, generate training tiles, or produce a prediction.

---

## Project checklist

- [ ] Loaded your own candidate dataset (or datasets) from L02 onto an interactive map
- [ ] Checked visually whether any labels or annotations you have align with the source imagery
- [ ] Chosen a band combination or colormap that best reveals the pattern your project cares about
- [ ] Built at least one split-panel comparison relevant to your project (two dates, two sources, or labels vs. imagery)
- [ ] Applied the five-point best-practices self-check from the [previous page](07_visualisation-best-practices.md) to at least one map

---

## Decision points

**Which basemap fits your project by default?** If your project depends on visually verifying structures or land cover, a satellite basemap is usually the right default. If it depends on terrain or hydrology, a topographic basemap may serve you better.

**Which band combination or colormap best reveals your target?** If you are not sure yet, try at least two options side by side, as in the mini task on the [raster data page](02_raster-data-on-maps.md), and pick based on what you actually see rather than habit.

**What will you need to compare later, and can you set that comparison up now?** If your project involves change detection, before/after imagery, or model evaluation, a split-panel pattern you build now will still be useful once you have real predictions.

---

## Common pitfalls

- **Building one-off maps you cannot reuse.** If you find yourself rewriting the same `leafmap.Map()` setup repeatedly, turn it into a small function instead. This will save time from L04 onward.
- **Never checking label alignment before relying on labels.** If your project uses existing annotations (from OpenStreetMap, a government dataset, or elsewhere), verify them visually before treating them as ground truth.
- **Overloading a single map with every layer you have.** A map with too many simultaneous layers becomes as hard to read as no map at all. Split panels or separate maps are often clearer than one crowded view.

---

## Mini deliverable

Produce a short **Project Visualization Notebook** containing:

1. An interactive map of your project's study area with an appropriate basemap.
2. At least one raster or vector layer from your own candidate data, styled deliberately (not left at default settings).
3. One split-panel comparison relevant to your project question.
4. A two- or three-sentence note on what the visualization revealed, including anything that looked wrong or unexpected.

Keep this notebook. You will extend it directly in [L04 – Data preprocessing](../04_data-preprocessing.md) and again once you generate training tiles in [L05 – Training data](../05_training-data.md).

---

## Reflection questions

- Did anything about your data look different once you actually visualized it, compared to what you expected from L02?
- If you have candidate labels, did they align cleanly with your imagery, or did you spot a misalignment worth investigating further?
- Which visualization technique from this lesson do you expect to use most often for your specific project, and why?
- If you had to show one map from this lesson to someone unfamiliar with your project, which one would you choose, and what would you need to add (a legend, a caption, a basemap) to make it understandable to them?