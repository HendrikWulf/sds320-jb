---
site:
  outline_maxdepth: 2
---

# Planetary Computer maps

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Visualising STAC items directly from a cloud catalogue, without downloading first
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why visualise before downloading

In [L02 – Working with remote sensing data](../L02/02_remote-sensing-data.md), you searched the {term}`Planetary Computer` {term}`SpatioTemporal Asset Catalog (STAC)` catalogue and downloaded a spatial subset of a matching scene. Before committing to a download, it is often useful to preview a candidate item directly on a map — checking cloud cover, framing, or general scene quality — without moving any data onto your machine.

The `geoai` package provides `view_pc_item()` for exactly this: it renders a STAC item as a map layer by requesting rendered tiles from the Planetary Computer, the same approach used by `add_stac_layer()` in `leafmap`.

---

## 2. Visualising a single item

```python
import geoai

geoai.view_pc_item(
    collection="sentinel-2-l2a",
    item="S2A_MSIL2A_20230715T101031_R022_T33TUL_20230715T182432",
    assets=["B04", "B03", "B02"],  # red, green, blue
)
```

This produces an interactive map centred on the item's footprint, rendering the requested bands as a true-colour composite. The exact item ID will differ depending on your STAC search results — reuse the search pattern from [L02](../L02/02_remote-sensing-data.md) to find one for your own area and date.

```{tip}
Requesting `assets=["B04", "B03", "B02"]` for Sentinel-2 gives a true-colour composite. Swap in different bands, for example `["B08", "B04", "B03"]`, for a false-colour composite that highlights vegetation.
```

---

## 3. Comparing multiple items

`view_pc_items()` (plural) accepts a list of item IDs and layers them on the same map, which is useful for a quick visual comparison across dates before deciding which scene to download:

```python
geoai.view_pc_items(
    collection="sentinel-2-l2a",
    items=[
        "S2A_MSIL2A_20230715T101031_R022_T33TUL_20230715T182432",
        "S2B_MSIL2A_20230815T101029_R022_T33TUL_20230815T170247",
    ],
    assets=["B04", "B03", "B02"],
)
```

Each item is added as a separately toggleable layer, so you can switch between dates using the map's layer control.

---

## 4. When to use this versus `leafmap.add_stac_layer()`

`leafmap.Map.add_stac_layer()` is the more general-purpose version of the same idea and works with any STAC-compliant catalogue, not only the Planetary Computer. `geoai.view_pc_item()` is a convenience wrapper specifically tuned for Planetary Computer collections and asset naming. For this course, prefer `view_pc_item()`/`view_pc_items()` when working with Planetary Computer data, and fall back to `add_stac_layer()` for other STAC catalogues, such as the swisstopo STAC endpoint used in [L02](../L02/02_remote-sensing-data.md).

---

## 5. Key takeaways

* `geoai.view_pc_item()` renders a Planetary Computer STAC item on an interactive map without downloading it.
* `assets` selects which bands to render, letting you preview true-colour or false-colour composites.
* `view_pc_items()` compares multiple items (for example, different dates) as toggleable layers on one map.
* Use `leafmap.add_stac_layer()` for STAC catalogues outside the Planetary Computer.
