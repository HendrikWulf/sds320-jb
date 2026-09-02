---
site:
  outline_maxdepth: 2
---

# Quality and splits

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Checking label quality and organising data for honest model evaluation
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

A dataset that looks fine at a glance can still have systematic problems that quietly limit model performance, incomplete labels, misaligned masks, or a class imbalance so severe the model learns to ignore the class you actually care about. Separately, how you split your data into training, validation, and test sets can make a model look far better than it actually is, if you are not careful about spatial separation.

---

## 2. Core ideas

Data-quality problems in GeoAI training sets tend to fall into a recognizable set of categories. A weak point anywhere in this chain can affect model performance.

```text
source imagery quality → label alignment → label completeness → class balance → tile boundaries → image-mask pairing → split strategy → documentation
```

In general machine learning, training data fit model parameters, validation data support model selection and tuning, and test data provide a final held-out evaluation. In spatial projects, this logic still applies, but the split must also account for spatial dependence. Geospatial train/validation/test splits need spatial separation, not just random shuffling, to give an honest estimate of performance.

---

## 3. Quality workflow

### A. Check label quality

| Issue | Why it matters | First check |
| --- | --- | --- |
| Incomplete annotations | The model learns that missing objects are background. | Inspect several tiles and look for visible but unlabelled objects. |
| Spatial misalignment | The model learns shifted boundaries. | Overlay labels on imagery and zoom in. |
| Class imbalance | The model may ignore rare classes. | Count pixels, objects or chips per class. |
| Edge effects | Objects cut by tile boundaries can confuse training. | Inspect chips near tile edges. |
| Outdated labels | The model learns from objects that changed or disappeared. | Compare imagery date and label source date. |
| Ambiguous classes | The model receives inconsistent examples. | Write clear class definitions and inspect edge cases. |

```{warning}
A label can be “official” and still be unsuitable for your project. Suitability depends on your imagery, date, scale, task and evaluation goal.
```

### B. Check class balance

Many spatial datasets are imbalanced. Background pixels may greatly outnumber building pixels. Common classes may dominate rare classes.

{term}`Accuracy` can be misleading in this situation. Later lessons will introduce task-specific metrics, but training-data preparation already needs basic class awareness.

Useful checks include:

- number of chips with and without target features,
- number of objects per class,
- approximate pixel counts per class,
- geographic distribution of rare classes,
- whether rare classes appear in training, validation and test areas.

Possible responses include:

- keeping some empty tiles but not too many,
- oversampling rare classes,
- using class weights or focal loss in later training,
- reducing the number of classes,
- collecting more labels for underrepresented classes.

### C. Check edge effects

Objects at chip edges may be incomplete. This matters for buildings, roads, fields, vehicles and other structured features.

Overlap helps because the same object may appear fully in another chip. However, overlap increases the number of chips and can create strong similarity between neighbouring examples.

Use overlap deliberately:

```text
more overlap
→ fewer cut objects
→ more files
→ more spatial similarity between chips
```

For evaluation, do not let overlapping chips from the same area appear across training and validation sets.

### D. Organise the dataset

For segmentation-style datasets, a common structure is:

```text
dataset/
  images/
    tile_000000.tif
    tile_000001.tif
  masks/
    tile_000000.tif
    tile_000001.tif
```

For separate splits:

```text
dataset/
  train/
    images/
    masks/
  val/
    images/
    masks/
  test/
    images/
    masks/
```

For detection datasets, the layout may follow formats such as {term}`YOLO`:

```text
dataset/
  images/
    train/
    val/
  labels/
    train/
    val/
  data.yaml
```

The main principle is simple: image and label files must be paired unambiguously.

### E. Split spatially

In ordinary machine learning examples, data are often randomly split into training and validation sets. For spatial data, this can be risky.

Nearby chips are often similar because they share the same landscape, lighting, sensor, season and annotation style. Randomly splitting neighbouring chips can create {term}`Data Leakage`: the validation set may be too similar to the training set.

Better options include:

| Split strategy | Use when |
| --- | --- |
| Region-based split | You have several geographic areas. |
| Scene-based split | You have multiple satellite or aerial scenes. |
| Spatial buffer | You must split within one scene but can leave distance between areas. |
| Time-based split | You want to test generalisation to another date. |

Common split ratios such as 70/15/15 or 80/10/10 are starting points, not rules. The spatial logic matters more than the exact ratio.

---

## 4. Python reactivation

You can quickly inspect generated folders with {term}`Pathlib`.

```{code-cell} python
from pathlib import Path

output_dir = Path("output")
image_tiles = list(output_dir.glob("images/*.tif"))
mask_tiles = list(output_dir.glob("masks/*.tif"))

print("Image tiles:", len(image_tiles))
print("Mask tiles:", len(mask_tiles))
```

Matching counts are not enough, but they are a useful first check.

You can also store split logic explicitly:

```{code-cell} python
split_plan = {
    "strategy": "region-based",
    "train": "western part of study area",
    "validation": "central part with spatial buffer",
    "test": "eastern part of study area",
    "reason": "reduce leakage between neighbouring chips",
}

split_plan
```

---

## 5. Common pitfalls

- **Splitting after tiling, at random.** As above, this is the single most common source of an artificially optimistic validation score in student projects.
- **Not tracking class balance until after training already looks disappointing.** Check it as part of your quality-control pass, before you invest time in training.
- **Using the test set to make any decisions before the final evaluation.** Even a single peek to "just check" quietly compromises how honest that final number really is.
- **Inconsistent file naming between images and labels.** Decide on a convention early and stick to it across your whole pipeline.

---

## 6. Mini task

For your own project's tiles (or the sample tiles from earlier pages), describe how you would split them into training, validation, and test sets while avoiding spatial leakage, and name the split ratio you would use.

:::{note} Sample solution
:class: dropdown

For a citywide building-footprint project covering four distinct neighborhoods: assign two neighborhoods to training, one to validation, and one to test, rather than mixing tiles from all four neighborhoods and splitting randomly. This keeps roughly a 50/25/25 split by area, close enough to common ratios, while guaranteeing that no validation or test tile is spatially adjacent to a training tile.
:::

---

## 7. Key takeaways

- Check systematically for incomplete labels, misalignment, class imbalance, and edge effects before trusting a generated dataset.
- Random, tile-level splits usually leak information because of spatial autocorrelation between nearby tiles.
- Split at the region or scene level, with a spatial buffer if splitting within a single scene.
- The test set should stay untouched until your final evaluation; using it earlier undermines the honesty of that final check.
- Consistent file naming between images and labels is necessary for data loaders to pair them correctly.

## Further reading

- [Training, validation, and test data sets (Wikipedia)](https://en.wikipedia.org/wiki/Training,_validation,_and_test_data_sets) — a more formal treatment of why each of the three sets exists, the hold-out method, and why reusing the test set for tuning decisions undermines its purpose.
