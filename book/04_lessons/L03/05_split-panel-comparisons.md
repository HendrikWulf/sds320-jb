---
site:
  outline_maxdepth: 2
---

# Split-panel comparisons

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Comparing two datasets side by side with a draggable slider
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

A large share of GeoAI work is fundamentally comparative: {term}`change detection <Change Detection>` compares two dates, model evaluation compares predictions against {term}`ground truth <Ground Truth>`, and data selection compares imagery from different sensors or seasons. Viewing two layers stacked on top of each other, one at a time, makes it hard to judge fine spatial differences. A split view keeps both layers visible at the same extent and zoom level.

---

## 2. Core idea

A **split-panel map** places two layers side by side with a draggable divider, so both sides always share the same geographic extent and zoom, which keeps the comparison spatially honest.

---

## 3. Workflow

### A. Prepare sample data

This example compares SWISSIMAGE imagery, a height raster and building footprints.

```{code-cell} python
import geoai
import leafmap

swissimage_url = "https://data.source.coop/giuz/sds320/L03/data/willisau_2024_swissimage_rgb_subset.tif"
hag_url = "https://data.source.coop/giuz/sds320/L03/data/willisau_height_above_ground_ndsm.tif"
buildings_url = ("https://data.source.coop/giuz/sds320/L03/data/willisau_overture_buildings_subset.geojson")

swissimage_path = geoai.download_file(swissimage_url)
hag_path = geoai.download_file(hag_url)
buildings_path = geoai.download_file(buildings_url)
```

### B. Build a basic split comparison

`split_map()` accepts common layer sources, including raster and vector files, with `left_args` and `right_args` for per-side rendering options.

```{code-cell} python
m = leafmap.Map()
m.split_map(
    left_layer=swissimage_path,
    right_layer=hag_path,
    left_args={"indexes": [1, 2, 3]},
    right_args={"vmin": 0, "vmax": 10, "cmap": "plasma"},
    left_label="SWISSIMAGE (true color)",
    right_label="Height Above Ground",
)
m
```

Dragging the slider left and right reveals each layer. Areas with tall structures in the imagery should line up with high values in the height raster; if they consistently do not, that is a sign the two datasets are misaligned.

### C. Split comparison for quality control

The same technique works for comparing vector annotations against their source imagery, which is one of the most useful checks you can run before trusting a set of labels.

```{code-cell} python
m2 = leafmap.Map()
m2.split_map(
    left_layer=buildings_path,
    right_layer=swissimage_path,
    left_args={"style": {"color": "red", "fillOpacity": 0.2}},
)
m2
```

```{admonition} What a split view is good for
:class: hint
Reach for a split-panel comparison whenever you are asking "do these two things actually match up in space," rather than "what does this one thing look like." The former is a comparison question; the latter is answered by the single-layer techniques from the earlier pages in this lesson.
```

---

## 4. Key takeaways

- Split-panel maps keep two layers at the same extent and zoom, which makes spatial comparisons more reliable than toggling layers on and off.
- `split_map()` and `geoai.create_split_map()` both support raster and vector sources with per-side styling arguments.
- Split views are well suited to alignment checks; they do not replace quantitative evaluation metrics.
- Consistent styling on both sides is necessary for a comparison to reflect real differences in the data rather than differences in how it was drawn.
