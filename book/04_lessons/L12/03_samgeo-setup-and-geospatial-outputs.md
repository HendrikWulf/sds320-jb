---
site:
  outline_maxdepth: 2
---

# samgeo setup

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Setting up samgeo and getting georeferenced outputs, not plain image masks
</div>
<!-- markdownlint-enable MD033 -->

---

You understand SAM's architecture conceptually. This page covers the practical bridge between that architecture and your actual geospatial data: the `segment-geospatial` package, environment setup, and the access step you should not leave until the last minute.

---

## 1. Motivation

SAM itself has no built-in notion of coordinate reference systems or georeferenced rasters; it works on plain images. `samgeo` is what makes SAM's output usable in a GIS workflow at all, and getting the setup and access steps right here saves you from a frustrating interruption partway through the hands-on pages ahead.

---

## 2. Core idea

**{term}`segment-geospatial (samgeo) <segment-geospatial>`** wraps SAM's segmentation capability with geospatial input and output handling: it reads multiband GeoTIFF files, preserves {term}`coordinate reference system <Coordinate Reference System>` information through the entire pipeline, and exports results as standard vector formats. This lesson uses its `SamGeo3` class, built for SAM 3 specifically, and its video counterpart, `SamGeo3Video`, covered later in this lesson.

---

## 3. Workflow

### A. Install and import

```{code-cell} python
# %pip install geoai-py "segment-geospatial[samgeo3]"
import os
import geoai
import leafmap
from samgeo import SamGeo3, SamGeo3Video, download_file, show_image
from samgeo.common import raster_to_vector, regularize
```

`samgeo`'s SAM 3 support is an optional extra (`[samgeo3]`), installed alongside the base package. `raster_to_vector` and `regularize`, used later on the building-extraction page, live in `samgeo.common` rather than the top-level package.

### B. Request Hugging Face access

```{admonition} Do this before you need it
:class: warning
SAM 3 requires access approval through Hugging Face before first use. Request access at the SAM 3 model page, and expect the approval to take some time, not be instant. Requesting access well before you plan to run any code in this lesson avoids an inconvenient wait partway through a work session.
```

Once access is granted, authenticate once per environment:

```{code-cell} python
# from huggingface_hub import login
# login()
```

This uses the same {term}`Hugging Face Hub <Hugging Face Hub>` authentication pattern from earlier lessons, just for downloading a gated model rather than publishing one.

### C. Initialize SamGeo3 and load an image

```{code-cell} python
sam3 = SamGeo3(backend="meta", device=None, checkpoint_path=None, load_from_HF=True)
sam3.set_image("path/to/your/image.tif")
```

`backend="meta"` uses the original Meta implementation; a `"transformers"` backend is also available and is required specifically for the interactive map interface covered later in this lesson. `set_image()` is the step that runs the image encoder and caches the {term}`image embedding <Image Embedding>` from the previous page; everything that follows reuses this cached result.

### D. Understand the geospatial outputs

When the input is a georeferenced GeoTIFF, `samgeo`'s outputs preserve that georeferencing automatically: saved mask rasters carry the same CRS as the input, confidence-score rasters (saved alongside masks) are pixel-aligned with them, and vector exports (GeoPackage, Shapefile, or GeoJSON) are immediately usable in GIS software without a separate registration step. You will use each of these output types directly starting on the prompting page ahead.

---

## 4. Python reactivation

The `#` at the start of the `%pip install` and `login()` lines marks them as comments in the code cells above, meaning they will not execute automatically; they are shown as a reminder of the one-time setup steps rather than something to rerun every time. Uncomment and run them yourself the first time you set up this environment.

---

## 5. Common pitfalls

- **Waiting until you need SAM 3 to request Hugging Face access.** Approval is not instant; request it ahead of your first hands-on session with this lesson.
- **Assuming any backend works for every feature.** The interactive map interface later in this lesson specifically requires the `"transformers"` backend; check which backend a given workflow needs before you start.
- **Feeding SAM a non-georeferenced image and expecting georeferenced output.** Preserved CRS information depends on the input actually being a georeferenced GeoTIFF; a plain JPEG or PNG input, used later for the truck example, will not produce georeferenced output.
- **Re-initializing `SamGeo3` unnecessarily.** As the previous page emphasized, the expensive step is image encoding, not model initialization alone, but repeatedly re-creating the detector object and reloading images still wastes time you do not need to spend.

---

## 6. Mini task

You plan to segment a georeferenced Sentinel-2 scene and also want to use the interactive map interface for exploratory work later. What backend would you initialize `SamGeo3` with, and why check this now rather than partway through your workflow?

:::{dropdown} Sample solution
:class: note

Since the interactive map interface specifically requires the `"transformers"` backend, initializing `SamGeo3` with `backend="transformers"` from the start avoids needing to re-initialize the model partway through your workflow once you reach the interactive step. Checking this upfront, rather than discovering the requirement mid-session, saves you from redoing earlier steps (including re-encoding the image) under a different backend.
:::

---

## 7. Key takeaways

- `segment-geospatial` (`samgeo`) bridges SAM to georeferenced data, preserving CRS through masks, confidence rasters, and vector exports.
- SAM 3 requires Hugging Face access approval that is not instant; request it well ahead of when you plan to use it.
- The `backend` parameter (`"meta"` or `"transformers"`) affects which features are available; check requirements before starting a workflow.
- Georeferenced output depends on georeferenced input; a plain image file will not produce a georeferenced result.

### Further reading

- [samgeo documentation](https://samgeo.gishub.org/) — the full reference documentation for the `segment-geospatial` package used throughout this lesson.
