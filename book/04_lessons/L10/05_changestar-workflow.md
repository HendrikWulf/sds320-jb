---
site:
  outline_maxdepth: 2
---

# ChangeStar workflow

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A practical, end-to-end workflow using the ChangeStar model
</div>
<!-- markdownlint-enable MD033 -->

---

You now understand the siamese-network idea conceptually. This page runs a specific, pre-trained implementation of it, ChangeStar, on real NAIP aerial imagery, before the next two pages dig into its output and tuning options.

---

## 1. Motivation

This is the page where the previous page's architecture concepts become an actual working detector. ChangeStar is also a good illustration of a broader pattern: rather than training a change detection model from scratch, you are using one that arrives already pretrained to generalize across regions, similar in spirit to the pre-trained segmentation models from L07.

---

## 2. Core idea

**{term}`ChangeStar`** jointly performs change detection and semantic segmentation: instead of only producing a binary change mask, it also outputs building footprint segmentation for each of the two input dates. Knowing what exists at each time step is part of what helps the model determine what has changed between them, rather than treating change detection as a separate problem from understanding either individual scene.

---

## 3. Workflow

### A. List available model variants

```{code-cell} python
from geoai.change_detection import (
    ChangeStarDetection,
    changestar_detect,
    list_changestar_models,
)

models = list_changestar_models()
for short_name, full_name in models.items():
    print(f"{short_name:30s} -> {full_name}")
```

ChangeStar ships several variants, trained with different Changen2 pretraining strategies. The naming convention encodes the pretraining stage (for example, `s1` or `s9`) and the backbone architecture (`vitb` for ViT-Base, `vitl` for ViT-Large). You will compare specific variants directly on a later page; for now, it is enough to know that this naming pattern exists.

### B. Set up an output location

```{code-cell} python
import geoai
from pathlib import Path

device = geoai.get_device()
print(f"Using device: {device}")

out_folder = "changestar_results"
Path(out_folder).mkdir(exist_ok=True)
```

`get_device()` checks whether a {term}`GPU` is available and reports which compute device will be used; this does not require any action from you, but it is useful to confirm before running a full-scene prediction, since GPU-less runs are typically much slower.

### C. Download the sample imagery

```{code-cell} python
naip_2019_url = "https://data.source.coop/opengeos/geoai/las_vegas_naip_2019_a.tif"
naip_2022_url = "https://data.source.coop/opengeos/geoai/las_vegas_naip_2022_a.tif"

naip_2019_path = geoai.download_file(naip_2019_url)
naip_2022_path = geoai.download_file(naip_2022_url)
```

This is {term}`NAIP` aerial imagery over Las Vegas, Nevada, from 2019 and 2022, a period of substantial suburban development, which makes it a useful test case for building change specifically.

### D. Inspect the input pair

```{code-cell} python
geoai.create_split_map(
    left_layer=naip_2019_path,
    right_layer=naip_2022_path,
    left_label="NAIP 2019",
    right_label="NAIP 2022",
)
```

This {term}`split-panel map <Split-Panel Map>` lets you visually confirm the two images cover the same area and look broadly comparable, exactly the comparability check from two pages ago, before spending any time on prediction. Skipping this step means you might not notice an obvious alignment or coverage problem until much later.

### E. Initialize the model

```{code-cell} python
detector = ChangeStarDetection(model_name="s1_s1c1_vitb")
```

`s1_s1c1_vitb` uses a ViT-Base backbone with stage-1 Changen2 pretraining, a reasonable starting variant. Model weights download automatically the first time you use a given variant.

### F. Run change detection

```{code-cell} python
result = detector.predict(
    naip_2019_path,
    naip_2022_path,
    output_change=str(Path(out_folder) / "change_map.tif"),
    output_t1_semantic=str(Path(out_folder) / "t1_buildings.tif"),
    output_t2_semantic=str(Path(out_folder) / "t2_buildings.tif"),
    output_vector=str(Path(out_folder) / "changes.gpkg"),
)
```

`predict()` takes the two image paths and returns a dictionary of results, covered in detail on the next page. Large images are automatically split into overlapping tiles for processing and stitched back together with overlap averaging, the same {term}`tiled inference <Tiled Inference>` idea you have now seen across several lessons. Specifying the `output_*` paths saves georeferenced GeoTIFFs and a vector file directly to disk, which you will need for the export step two pages from now.

---

## 4. Python reactivation

`models.items()` iterates over a dictionary's key-value pairs together, the same pattern used for the `stats` dictionary in L09. `Path(out_folder).mkdir(exist_ok=True)` creates a directory if it does not already exist, and does nothing (rather than raising an error) if it does, a convenient pattern for output folders you might create more than once across a session.

---

## 5. Common pitfalls

- **Skipping the split-map inspection in step D.** This is the fastest way to catch a coverage or alignment problem before it wastes a full prediction run.
- **Choosing a model variant without understanding what the name encodes.** The pretraining stage and backbone size both affect behavior; the next page compares variants directly rather than asking you to guess.
- **Forgetting to specify `output_*` paths.** Without them, results exist only in memory as `result`, and nothing is saved to disk for later use or GIS export.
- **Assuming a GPU is required.** `get_device()` will report what is actually available; a CPU run is slower but still functional for a lesson-scale example.

---

## 6. Mini task

Before running step F, predict which of ChangeStar's likely outputs (a change map, and per-date building segmentation) you expect to show the *most* visual change between 2019 and 2022 in a rapidly suburbanizing area like Las Vegas, and why.

:::{dropdown} Sample solution
:class: note

The change map itself should show the most visually obvious difference, since it is specifically designed to highlight areas of change, concentrating exactly where new construction occurred rather than spreading attention across the whole scene. The per-date building masks (T1 and T2) will individually look broadly similar to each other in already-developed areas, with the meaningful difference being the *new* buildings present in the T2 mask but absent from the T1 mask, which is precisely the comparison the change map is built to surface directly.
:::

---

## 7. Key takeaways

- ChangeStar jointly produces a change map and per-date building segmentation, using per-date understanding to inform the change detection itself.
- Model variant names encode the Changen2 pretraining stage and backbone size; `s1_s1c1_vitb` is a reasonable starting point.
- Visually inspecting the input image pair with a split map before running prediction catches coverage and alignment problems early.
- Specifying `output_*` paths in `predict()` saves georeferenced results directly to disk, needed for later interpretation and export.

### Further reading

- OpenGeoAI, ["ChangeStar"](https://opengeoai.org/examples/changestar/) — the full, runnable notebook this workflow is based on.
- OpenGeoAI, [Change Detection module documentation](https://opengeoai.org/change_detection/) — reference documentation for the underlying `geoai.change_detection` module.
