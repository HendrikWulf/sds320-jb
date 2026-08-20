---
site:
  outline_maxdepth: 2
---

# Split-panel comparisons

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Comparing two layers side by side in a single interactive map
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why a split map

Toggling layers on and off with a layer control works well for a handful of layers, but it makes direct comparison awkward — you have to remember what the previous layer looked like. A split-panel map instead shows two layers at once, divided by a draggable slider, which is far more effective for questions like "what changed between these two dates" or "how do these two data sources differ over the same area".

---

## 2. A basic split map

`leafmap.Map.split_map()` builds a two-panel comparison from two named basemaps or layers:

```python
import leafmap

m = leafmap.Map(center=[47.37, 8.54], zoom=13)
m.split_map(
    left_layer="HYBRID",
    right_layer="OpenStreetMap",
    left_label="Satellite",
    right_label="Streets",
)
m
```

Dragging the slider that appears on the map reveals more of one panel and less of the other, which is an intuitive way to compare two representations of the same area.

---

## 3. Comparing two rasters

The same pattern works with your own raster files by passing dictionaries of layer arguments instead of basemap names:

```python
m = leafmap.Map(center=[47.37, 8.54], zoom=13)
m.split_map(
    left_layer="path/to/before.tif",
    right_layer="path/to/after.tif",
    left_label="Before",
    right_label="After",
)
```

This is a natural preview of the [change detection lesson](../10_change-detection.md) later in this book — comparing two dates visually is often the first, fastest check before running any change-detection algorithm.

---

## 4. Using `geoai.create_split_map()`

`geoai` provides an equivalent function, `create_split_map()`, with the same argument names, which is useful when you are already working within the `geoai` ecosystem and want a consistent import pattern:

```python
import geoai

geoai.create_split_map(
    left_layer="path/to/before.tif",
    right_layer="path/to/after.tif",
    left_label="Before",
    right_label="After",
    center=[47.37, 8.54],
    zoom=13,
)
```

---

## 5. Key takeaways

* A split-panel map compares two layers directly with a draggable slider, which is often clearer than toggling layers on and off.
* `split_map()` (leafmap) and `create_split_map()` (`geoai`) accept either named basemaps or your own raster/vector layers.
* `left_label`/`right_label` make the comparison legible to a reader who was not there when you built the map.
* This technique previews the visual-comparison step that precedes most change-detection work later in this book.
