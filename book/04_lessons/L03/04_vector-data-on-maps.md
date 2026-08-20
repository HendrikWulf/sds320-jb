---
site:
  outline_maxdepth: 2
---

# Vector data on maps

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Adding, styling, and inspecting vector layers interactively
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Adding a GeoDataFrame to a map

If you already have vector data loaded as a {term}`GeoDataFrame` — for example the Overture Maps buildings or OpenStreetMap roads from [L02 – Adding vector and contextual data](../L02/03_vector-context-data.md) — `add_gdf()` adds it directly:

```python
import leafmap
import geopandas as gpd

buildings = gpd.read_parquet("buildings.parquet")

m = leafmap.Map()
m.add_gdf(buildings, layer_name="Buildings")
m.add_layer_control()
m
```

If your data lives in a file rather than already in memory, `add_vector()` reads and adds it in one step, and additionally supports filtering by `bbox` at load time:

```python
m.add_vector(
    "buildings.geojson",
    layer_name="Buildings",
    bbox=(8.53, 47.37, 8.54, 47.38),
)
```

---

## 2. Styling and hover behaviour

Both functions accept a `style` dictionary (standard Leaflet path style options) and a `hover_style` for mouse-over feedback:

```python
m.add_gdf(
    buildings,
    layer_name="Buildings",
    style={"color": "#3388ff", "weight": 1, "fillOpacity": 0.3},
    hover_style={"fillOpacity": 0.7},
    info_mode="on_hover",
)
```

`info_mode="on_hover"` shows attribute values in a popup as the cursor moves over a feature; `info_mode="on_click"` shows them only when a feature is clicked, which is often less distracting for a busy layer.

---

## 3. Colouring by an attribute

For a categorical or numeric attribute, pass a list of `fill_colors` matching the number of categories, or use `style_callback` for full control over per-feature styling:

```python
def style_by_class(feature):
    suitability = feature["properties"].get("klasse_text", "")
    colors = {"Top": "#1a9850", "Middle": "#fee08b", "Low": "#d73027"}
    return {"fillColor": colors.get(suitability, "#999999"), "weight": 0.5, "fillOpacity": 0.6}

m.add_gdf(roofs, layer_name="Roof suitability", style_callback=style_by_class)
```

This reuses the roof-suitability data from the [Swiss geodata example in L02](../L02/03_vector-context-data.md), now styled by suitability class instead of printed as a table.

---

## 4. A quick static alternative

Not every check needs an interactive map. `geoai.view_vector()` produces a static, styled plot in one call — useful for a fast sanity check inside a script, or for a figure you want to save directly:

```python
import geoai

geoai.view_vector(
    buildings,
    column="height",
    cmap="viridis",
    title="Building heights",
)
```

---

## 5. Key takeaways

* `add_gdf()` adds an in-memory `GeoDataFrame`; `add_vector()` reads a file directly and can filter by `bbox` at load time.
* `style`, `hover_style`, and `style_callback` control how features are rendered and how they respond to the cursor.
* `style_callback` is the most flexible option when colour should depend on an attribute value.
* `geoai.view_vector()` is a fast, static alternative when a full interactive map is not needed.
