---
site:
  outline_maxdepth: 2
---

# Tuning thresholds

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Tuning thresholds, comparing model variants and exporting results
</div>
<!-- markdownlint-enable MD033 -->

---

You can now read every piece of a ChangeStar result. This page covers the decisions that shape what ends up in `change_map` in the first place, model variant and threshold, and finishes by getting your results into files a GIS can actually use.

---

## 1. Motivation

`s1_s1c1_vitb` and a threshold of 0.5 are defaults, not guaranteed to be the right choice for every project. Knowing how to compare variants and adjust sensitivity, and how to export the result properly, is what turns this lesson's example into a workflow you can adapt for your own study area.

---

## 2. Core idea

Two independent choices shape ChangeStar's output: which pretrained model variant you use, and what probability threshold converts continuous change probabilities into a binary map. Both are worth testing explicitly rather than accepting defaults, and once you have settled on a result you trust, exporting it as georeferenced files is what makes it usable outside this notebook.

---

## 3. Workflow

### A. A quick convenience function

```{code-cell} python
from geoai.change_detection import changestar_detect

result2 = changestar_detect(
    naip_2019_path,
    naip_2022_path,
    model_name="s1_s1c1_vitb",
    output_change="changestar_results/change_map_v2.tif",
)

print(f"Change pixels: {result2['change_map'].sum():,}")
print(f"Changed area: {100 * result2['change_map'].sum() / result2['change_map'].size:.2f}%")
```

`changestar_detect()` initializes the model, runs prediction, and returns the result dictionary in a single call, useful for a quick one-off analysis where you do not need to reuse the same detector across multiple image pairs.

### B. Comparing model variants

```{code-cell} python
import numpy as np

detector_s9 = ChangeStarDetection(model_name="s9_s9c1_vitb")
result_s9 = detector_s9.predict(naip_2019_path, naip_2022_path)

fig, axes = plt.subplots(1, 3, figsize=(18, 6))
axes[0].imshow(result["change_map"], cmap="gray")
axes[0].set_title(f"S1 Model\n(Changed pixels: {result['change_map'].sum():,})")
axes[0].axis("off")

axes[1].imshow(result_s9["change_map"], cmap="gray")
axes[1].set_title(f"S9 Model\n(Changed pixels: {result_s9['change_map'].sum():,})")
axes[1].axis("off")

combined = np.zeros((*result["change_map"].shape, 3), dtype=np.uint8)
combined[result["change_map"] == 1, 0] = 255  # S1 in red
combined[result_s9["change_map"] == 1, 2] = 255  # S9 in blue
both = (result["change_map"] == 1) & (result_s9["change_map"] == 1)
combined[both] = [255, 0, 255]  # agreement in magenta

axes[2].imshow(combined)
axes[2].set_title("Comparison\n(Red=S1 only, Blue=S9 only, Magenta=Both)")
axes[2].axis("off")
plt.tight_layout()
plt.show()
```

Different Changen2 pretraining stages can produce meaningfully different results on the same input. The three-color comparison makes disagreement visible directly: large magenta regions indicate the two variants agree, while substantial red-only or blue-only areas indicate a model-specific difference worth investigating before deciding which variant to trust for your study area.

### C. Adjusting the detection threshold

```{code-cell} python
thresholds = [0.3, 0.5, 0.7]
fig, axes = plt.subplots(1, len(thresholds), figsize=(18, 6))

for ax, thresh in zip(axes, thresholds):
    result_t = detector.predict(naip_2019_path, naip_2022_path, threshold=thresh)
    ax.imshow(result_t["change_map"], cmap="gray")
    ax.set_title(f"Threshold = {thresh}\n(Changed pixels: {result_t['change_map'].sum():,})")
    ax.axis("off")

plt.tight_layout()
plt.show()
```

The default threshold of 0.5 converts `change_prob` into `change_map`. Lowering it increases sensitivity, detecting more changes but also more false alarms, while raising it increases specificity, keeping only higher-confidence detections at the cost of missing subtler changes. This is the same underlying trade-off you tuned via `confidence_threshold` in L06's detection workflow, applied here to change probability instead.

### D. Viewing and using saved outputs

```{code-cell} python
import os

for f in sorted(os.listdir(out_folder)):
    fpath = os.path.join(out_folder, f)
    size_mb = os.path.getsize(fpath) / 1024 / 1024
    print(f"{f:40s} {size_mb:.2f} MB")
```

The `predict()` call from the workflow page saved georeferenced GeoTIFFs and a {term}`GeoPackage` vector file to `out_folder`, ready to load directly into any GIS software. You can also display a saved raster interactively without leaving the notebook:

```{code-cell} python
change_map_path = os.path.join(out_folder, "change_map.tif")
geoai.view_raster(
    change_map_path,
    nodata=0,
    cmap="Reds",
    opacity=0.8,
    basemap=naip_2019_path,
    backend="ipyleaflet",
)
```

---

## 4. Python reactivation

`zip(axes, thresholds)` pairs up two sequences (the subplot axes and the threshold values) so you can iterate over both together in one loop, the same pattern useful whenever you need to process two related lists in lockstep. `os.listdir()` and `os.path.getsize()` are standard file-system functions for listing a directory's contents and checking a file's size, useful for confirming your exported outputs actually saved as expected.

---

## 5. Common pitfalls

- **Treating the default threshold of 0.5 as universally correct.** As with any confidence threshold in this course, the right value depends on whether false positives or false negatives are more costly for your specific application.
- **Committing to one model variant without comparing at least one alternative.** The red/blue/magenta comparison in step B is a fast way to see whether variant choice actually matters for your study area.
- **Forgetting to specify an output directory that exists.** `Path(out_folder).mkdir(exist_ok=True)` from the workflow page handles this, but it is worth remembering if you restructure the code.
- **Treating exported GeoTIFFs as the only usable output.** The GeoPackage vector file is often more directly useful for GIS analysis (area calculations, joins with other layers) than the raster alone.

---

## 6. Mini task

Looking at the three-threshold comparison in step C, would you expect the number of changed pixels at `threshold=0.3` to be larger or smaller than at `threshold=0.7`? Explain your reasoning using the sensitivity/specificity framing from this page.

:::{dropdown} Sample solution
:class: note

The number of changed pixels at `threshold=0.3` should be larger. A lower threshold means more pixels with moderate change probability now qualify as "changed," increasing sensitivity (and detected count) at the cost of including more likely false positives. At `threshold=0.7`, only pixels with high change probability pass, reducing the count but increasing confidence in each individual detection that remains.
:::

---

## 7. Key takeaways

- `changestar_detect()` offers a one-call convenience path when you do not need to reuse a detector across multiple image pairs.
- Comparing model variants directly, rather than assuming a default is best, can reveal meaningful differences in detected change.
- The probability threshold trades detection sensitivity against specificity; there is no universally correct default.
- `predict()`'s saved GeoTIFFs and GeoPackage files are what make ChangeStar's output usable in an actual GIS workflow, beyond the notebook itself.
