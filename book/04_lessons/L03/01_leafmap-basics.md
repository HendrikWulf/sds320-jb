---
site:
  outline_maxdepth: 2
---

# Leafmap basics

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Creating interactive maps in Jupyter notebooks
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this page matters

In Lesson 02, you searched for datasets and created small spatial subsets. Here, you start inspecting those data interactively.

Every visualization technique in this lesson builds on the same starting point: a `leafmap` map object. Getting comfortable with creating and configuring one is a small investment that pays off across the rest of the course.

---

## 2. Core idea

{term}`Leafmap` gives you a Pythonic interface to an interactive map widget inside a Jupyter notebook. It is built on `ipyleaflet` but adds convenience functions for loading raster, vector, and cloud-hosted geospatial data, so you do not have to assemble those pieces yourself.

---

## 3. Create your first map

The basic object is `leafmap.Map()`.

```{code-cell} python
import leafmap

m = leafmap.Map(center=[41.38, 2.16], zoom=12, height="600px")  # centred near Barcelona
m
```

The `center` parameter uses a latitude-longitude pair. The `zoom` parameter controls how close the initial view is. The `height` parameter controls how much vertical space the map uses in the notebook.

```{tip}
Leafmap map centres use `[latitude, longitude]`. Many spatial searches use bounding boxes in `[west, south, east, north]` order. Mixing these orders is a common source of wrong locations.
```

---

## 4. Add basemaps

A {term}`Basemap` gives spatial context. Without it, your results may look like shapes floating in empty space. With it, you can recognise neighbourhoods, roads, terrain or land-cover context.

```{code-cell} python
m = leafmap.Map()
m.add_basemap("Esri.WorldImagery")
m
```

Different basemaps support different purposes.

| Basemap type | Useful for |
| --- | --- |
| Satellite imagery | Checking labels or predictions against visible ground features. |
| Street map | Orienting readers with roads, places and neighbourhood names. |
| Terrain map | Understanding elevation, relief or hydrological context. |
| Light neutral map | Presenting thematic layers without visual clutter. |

You can also add more than one basemap and switch between them with the layer control.

```{code-cell} python
m = leafmap.Map()
m.add_basemap("Esri.WorldImagery")
m.add_basemap("OpenTopoMap")
m
```

---

## 5. Inspect available basemaps

Leafmap provides many basemap options. You do not need to memorise them. You can inspect the list when needed.

```{code-cell} python
basemaps = list(leafmap.basemaps.keys())

print(f"Total basemaps: {len(basemaps)}")
print("First 10:", basemaps[:10])
```

Use this list to test basemaps for your project map. Choose one that helps interpretation rather than one that simply looks attractive.

---

## 6. Use maps as a project check

For your own project, a first map should answer three basic questions:

1. Is the map centred on the right place?
2. Is the zoom level appropriate for the feature or pattern?
3. Does the basemap help interpret the area?

If the answer to any of these is unclear, fix the map before adding more layers.

---

## 7. Python reactivation

`leafmap.Map()` returns an object, and you build up your visualization by calling methods on that object (`m.add_basemap(...)`) rather than reassigning `m` each time. This is the same pattern you used with `matplotlib` axes objects in SDS210: create the object once, then call methods on it to add more content.

---

## 8. Common pitfalls

| Pitfall | How to avoid it |
| --- | --- |
| The map opens in the wrong place | Check whether coordinates are latitude-longitude or longitude-latitude. |
| The basemap distracts from the data | Use a simpler basemap or reduce layer opacity. |
| The map is too zoomed out | Match zoom level to the feature size you need to inspect. |
| Too many layers are added at once | Build the map gradually and check each layer. |
| Layer names are unclear | Use descriptive names that explain what each layer shows. |

---

## 9. Mini task

Create a map centered on your own project's rough study area (or a placeholder location if you do not have one yet). Add two different basemaps to it and use the layer control to switch between them.

:::{note} Sample solution
:class: dropdown

```{code-cell} python
import leafmap

m = leafmap.Map(center=[-3.0637, 37.3579], zoom=12, height="500px") 
m.add_basemap("Esri.WorldTerrain")
m.add_basemap("SwissFederalGeoportal.SWISSIMAGE")
m
```

Switching between "Esri.WorldImagery" and "OpenTopoMap" in the layer control shows the same area once as satellite imagery and once as a topographic map, which is a quick way to decide which basemap best supports your project's study area.
:::

---

## 10. Key takeaways

- Leafmap creates interactive maps directly in notebooks.
- Basemaps provide spatial context for data inspection.
- Map centres use latitude-longitude order.
- Interactive maps are useful for debugging and project decisions, not only final presentation.
- Start simple before adding raster, vector or model layers.
