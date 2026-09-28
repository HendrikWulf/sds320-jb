---
site:
  outline_maxdepth: 2
---

# Visualizing model results

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Overlaying prediction-style outputs on source imagery
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

A single accuracy number tells you how a model performs on average. It does not tell you *where* it fails. A model can post a high overall accuracy score while consistently missing buildings in shadow, confusing parking lots with rooftops, or producing jagged boundaries along water edges. These spatial error patterns are invisible in a summary statistic and immediately visible on a map.

---

## 2. Core idea

After running a GeoAI model, you typically have outputs as prediction rasters (segmentation masks) or vector files (detected objects). Overlaying these on the source imagery with partial transparency lets you assess whether the highlighted features actually correspond to real objects on the ground.

---

## 3. Workflow

### A. Prepare sample data

This example uses a rasterised building mask as a stand-in for a model prediction.

```{code-cell} python
from pathlib import Path

import geoai
import leafmap.foliumap as leafmap

DATA_DIR = Path("data/raw")
DATA_DIR.mkdir(parents=True, exist_ok=True)

swissimage_url = "https://data.source.coop/giuz/sds320/L03/data/willisau_2024_swissimage_rgb_subset.tif"
buildings_mask_url = "https://data.source.coop/giuz/sds320/L03/data/willisau_OSM_buildings_mask.tif"
buildings_url = "https://data.source.coop/giuz/sds320/L03/data/willisau_overture_buildings_subset.geojson"

swissimage_path = geoai.download_file(swissimage_url, output_path=str(DATA_DIR / Path(swissimage_url).name))
mask_path = geoai.download_file(buildings_mask_url, output_path=str(DATA_DIR / Path(buildings_mask_url).name))
buildings_path = geoai.download_file(buildings_url, output_path=str(DATA_DIR / Path(buildings_url).name))
```

### B. Overlay a mask on imagery

We do not have a trained model yet, so this example uses the same rasterized building {term}`mask <Mask>` from earlier pages the way you would use a real segmentation prediction later in the course.

```{code-cell} python
m = leafmap.Map()
m.add_raster(swissimage_path, layer_name="SWISSIMAGE imagery")
m.add_raster(
    mask_path,
    opacity=0.8,
    nodata=0,
    layer_name="Building mask",
)
m
```

Setting `opacity` below 1 lets the underlying imagery show through, so you can judge whether the highlighted pixels line up with real structures. The `nodata=0` argument makes background pixels (value 0) transparent, so only the positive class is drawn on top of the imagery.

### C. Compare labels and imagery

Before comparing a prediction to a reference label, confirm the reference label itself is trustworthy. With the standard, ipyleaflet-based `leafmap.Map`, `split_map()` would take the building-footprint GeoJSON directly, just like a raster file. This page uses `leafmap.foliumap` so the map renders correctly on the built book website, and that backend's `split_map()` only synchronizes raster tile layers; overlaying both layers on one map instead, as in the [previous page](05_split-panel-comparisons.md), works just as well for this check.

```{code-cell} python
m2 = leafmap.Map()
m2.add_raster(swissimage_path, layer_name="SWISSIMAGE imagery")
m2.add_geojson(
    buildings_path,
    layer_name="Building labels",
    style={"color": "red", "fillOpacity": 0.2},
)
m2
```

Once the reference labels look correct, you can substitute a real prediction layer into exactly the same comparison pattern later in the course.

```{admonition} A workflow you will reuse
:class: important
This overlay-with-transparency pattern is the same one you will use in [L06 – Object detection](../06_object-detection.md) and beyond, once you have actual predictions instead of a stand-in mask. Learning it now with a simple example means you will not have to relearn it under time pressure later.
```

---

## 4. Key takeaways

- Overlaying prediction-style outputs on source imagery reveals *where* and *why* a model fails, not just how often.
- Partial opacity and a `nodata` setting keep the source imagery visible under a raster overlay.
- Verifying reference labels visually, before comparing predictions to them, catches label problems that would otherwise look like model errors.
