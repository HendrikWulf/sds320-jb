---
site:
  outline_maxdepth: 2
---

# Leafmap basics

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Creating your first interactive map in a notebook
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why leafmap

{term}`Leafmap` is a Python package that wraps interactive mapping libraries (Leaflet.js, via `folium` or `ipyleaflet`) with a notebook-friendly API. It is the interactive mapping layer this book builds on, and the `geoai` package's own map helpers, covered in the following pages, extend it further for GeoAI-specific tasks.

---

## 2. Your first map

```python
import leafmap

m = leafmap.Map(center=[47.37, 8.54], zoom=12)  # centred near Zurich
m
```

Displaying `m` at the end of a notebook cell renders an interactive, pannable and zoomable map widget directly in the notebook.

---

## 3. Choosing a basemap

By default, `leafmap.Map()` uses a hybrid satellite basemap. You can switch to any of its built-in basemaps with `add_basemap()`:

```python
m = leafmap.Map(center=[47.37, 8.54], zoom=12)
m.add_basemap("OpenStreetMap")
```

Common choices include `"OpenStreetMap"`, `"HYBRID"`, `"SATELLITE"`, `"TERRAIN"`, and `"OpenTopoMap"`. Choose a basemap that supports your data rather than distracts from it — a busy street-map basemap can crowd out a subtle raster layer, while a plain terrain or satellite basemap often works better as background.

```{tip}
You can call `add_basemap()` more than once to layer multiple basemap options, then toggle between them with the layer control described below.
```

---

## 4. Adding a layer control

Once you have more than one layer on a map, add a layer control so viewers can toggle layers on and off:

```python
m.add_layer_control(position="topright")
```

Later pages in this lesson add raster, vector, and STAC layers to a map like this one — the pattern is always the same: create a `Map`, add one or more layers, optionally add a layer control, then display the map.

---

## 5. Key takeaways

* {term}`Leafmap` provides a notebook-friendly interactive map widget built on Leaflet.js.
* `leafmap.Map(center=..., zoom=...)` creates a map; displaying the object renders it.
* `add_basemap()` switches or layers basemaps; choose one that supports rather than competes with your data.
* `add_layer_control()` lets viewers toggle layers — useful as soon as a map has more than one layer.
