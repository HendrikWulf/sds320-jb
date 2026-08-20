---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Building a first interactive map for your own project data
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this page matters

This lesson should leave you with a working interactive map built from your own project data, not just from the example files used earlier in this lesson. Building one now — even a rough one — often surfaces data issues (misaligned CRS, an empty layer, an unexpectedly large file) well before you reach the modelling lessons.

---

## 2. Project checklist

- [ ] I built a `leafmap.Map()` with at least one basemap.
- [ ] I added at least one raster layer from my own project data.
- [ ] I added at least one vector layer from my own project data.
- [ ] I added a layer control so a viewer can toggle layers.
- [ ] I checked that my layers actually align spatially on the map (not just in code).
- [ ] I chose a colormap or style appropriate to my data type.

---

## 3. A minimal template

Adapt this template with your own file paths:

```python
import leafmap

m = leafmap.Map(center=[0, 0], zoom=10)  # replace with your area's centre

m.add_raster("path/to/your_raster.tif", layer_name="My raster")
m.add_vector("path/to/your_vector.geojson", layer_name="My vector")

m.add_layer_control()
m
```

If something looks wrong — a layer that does not appear, or appears in the wrong place — check the {term}`Coordinate Reference System (CRS)` of both layers first. This is the most common cause of a "missing" layer that is actually just rendered somewhere far outside the current map view.

---

## 4. Reflection questions

1. Did my raster and vector layers align spatially without extra reprojection steps? If not, what did I have to fix?
2. Which comparison — two dates, two sources, or two classes — would a split-panel map make clearer for my project?
3. Is my current map meant for exploration, or could a version of it end up in my final report?
4. What is one visualisation choice (colormap, basemap, styling) I want to revisit later, based on the [best practices page](07_visualisation-best-practices.md)?

---

## 5. Mini deliverable

Save a short notebook or script that builds one interactive map combining at least one raster and one vector layer from your own project. Note its location and a one-line description in your project notes, for example:

```text
notebooks/02_first_map.ipynb — first interactive view of [raster] and [vector] for [study area]
```

---

## 6. Key takeaways

* A first interactive map is a practical way to catch spatial alignment problems early.
* The `Map → add layer(s) → add layer control` pattern from this lesson covers most day-to-day mapping needs.
* Keep exploration maps rough; save polish for the figures that go into your final report.
* The next lessons move from viewing data to preparing it — starting with [L04 – Data preprocessing](../04_data-preprocessing.md).
