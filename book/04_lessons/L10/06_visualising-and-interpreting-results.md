---
site:
  outline_maxdepth: 2
---

# Interpreting results

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Visualising and interpreting a change map
</div>
<!-- markdownlint-enable MD033 -->

---

You ran ChangeStar on the previous page. This page works through exactly what `result` contains, and how to look at it critically, before the next page covers tuning and export.

---

## 1. Motivation

`result` is not just a change map; it is a small bundle of related rasters that, read together, tell you much more than any single one alone. Learning to read all of them is what lets you judge whether a detected change is trustworthy, rather than accepting a binary mask at face value.

---

## 2. Core idea

ChangeStar's `predict()` returns a dictionary with entries for the binary change map, the underlying change probability, and both hard and probabilistic building segmentation for each of the two input dates. Reading the {term}`probability map <Probability Map>` entries alongside the hard classifications is what lets you judge confidence, not just presence, of a detected change.

---

## 3. Workflow

### A. Inspect the result dictionary

```{code-cell} python
print("Result keys:", list(result.keys()))
for key, value in result.items():
    if hasattr(value, "shape"):
        print(f"  {key}: shape={value.shape}, dtype={value.dtype}")
    else:
        print(f"  {key}: {value}")
```

The dictionary contains `change_map` (the binary changed/unchanged raster), `change_prob` (the underlying per-pixel change probability, a probability map), `t1_semantic` and `t2_semantic` (hard building masks for each date), `t1_semantic_prob` and `t2_semantic_prob` (their corresponding probability rasters), and `num_semantic_classes` (how many segmentation classes the model distinguishes, here just building versus background).

### B. The five-panel overview

```{code-cell} python
fig = detector.visualize(
    naip_2019_path,
    naip_2022_path,
    result=result,
    figsize=(25, 10),
    title1="NAIP 2019",
    title2="NAIP 2022",
)
plt.show()
```

`visualize()` lays out the before image, the after image, both dates' building segmentation, and the change map side by side, exactly the kind of side-by-side comparison you have used throughout this course to sanity-check a model's output against the source imagery directly.

### C. Overlay visualization

```{code-cell} python
fig = detector.visualize_overlay(
    naip_2019_path,
    naip_2022_path,
    result=result,
    figsize=(20, 6),
    title1="NAIP 2019",
    title2="NAIP 2022",
)
plt.show()
```

`visualize_overlay()` draws detected buildings (in blue) and detected changes (in red) directly on top of the original imagery for each date, which makes it easier to judge, at a glance, whether flagged changes correspond to something visually plausible in the underlying scene, rather than examining an abstract mask in isolation.

### D. Reading probability rasters

```{admonition} A changed pixel with low confidence is a different situation than one with high confidence
:class: note
`change_map` gives you a firm yes/no per pixel, but `change_prob` tells you how confident that yes/no actually was. Two pixels can both show up as "changed" in `change_map` while one had a change probability of 0.95 and the other 0.51. Before treating a detected change area as reliable, especially a small or unusual-looking one, check its probability values rather than trusting the binary mask alone.
```

The same logic applies to `t1_semantic_prob` and `t2_semantic_prob`: if a building's segmentation confidence is low at one date but not the other, that alone might explain an apparent "change" that is really closer to model uncertainty about a borderline structure.

---

## 4. Python reactivation

`hasattr(value, "shape")` checks whether an object has a `shape` attribute before trying to use it, a defensive pattern useful when a dictionary mixes array-like values (with a shape) and plain values (like `num_semantic_classes`, a single integer) in one loop, avoiding an error on the entries that are not arrays.

---

## 5. Common pitfalls

- **Trusting `change_map` without checking `change_prob` for uncertain areas.** A binary mask alone hides exactly the confidence information that helps you judge which detections deserve more scrutiny.
- **Confusing T1 and T2.** T1 is always the earlier date, T2 the later one; mixing them up inverts your interpretation of what appeared versus what disappeared.
- **Interpreting an isolated changed pixel the same way as a large, spatially coherent change area.** The overlay visualization in step C is specifically useful for telling these apart visually.
- **Ignoring the semantic segmentation outputs when a change looks surprising.** If `t1_semantic` or `t2_semantic` looks wrong at a specific location, an apparent "change" there may reflect a segmentation error rather than a real surface change.

---

## 6. Mini task

You notice a small cluster of "changed" pixels in a residential area where the overlay visualization shows no building in either the 2019 or 2022 image. Using the outputs described on this page, describe two ways you would investigate before deciding whether this is a real detection or a likely error.

:::{dropdown} Sample solution
:class: note

First, check `change_prob` at that location: a low probability value would suggest the detection is not particularly confident and may be closer to noise. Second, check `t1_semantic_prob` and `t2_semantic_prob` at the same location: if the building segmentation confidence is also low or inconsistent there, that points toward a segmentation-driven artifact (the model being uncertain about the pixel's building status at one or both dates) rather than a genuine building change, since ChangeStar's change detection is informed by its own per-date segmentation.
:::

---

## 7. Key takeaways

- `result` bundles seven related outputs: a change map and probability, plus hard and probabilistic building segmentation for both dates.
- `visualize()` gives a full five-panel overview; `visualize_overlay()` draws detections directly on the source imagery for a more direct plausibility check.
- Probability rasters (`change_prob`, `t1_semantic_prob`, `t2_semantic_prob`) reveal confidence information a binary mask alone hides.
- Cross-checking an apparent change against the per-date segmentation outputs helps distinguish real change from a segmentation artifact.
