---
site:
  outline_maxdepth: 1
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning this lesson into concrete progress on your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

This lesson introduced the training-data pipeline: inspecting imagery and annotations, converting vector labels to raster masks, generating chips, running batch workflows, checking quality and planning spatial splits.

For your own project, the goal is not necessarily to create a full training dataset immediately. The goal is to understand what training data your project would need, what is already available, and what needs careful checking before modelling.

After this page, you should be able to explain:

```text
what the model should learn
→ what examples it needs
→ how labels are created or obtained
→ how quality will be checked
→ how data will be split and documented
```

---

## 2. Project checklist

Use this checklist for your project.

### Task and label needs

- [ ] I can name the GeoAI task I am preparing data for.
- [ ] I know what one training example looks like.
- [ ] I know whether I need class labels, masks, bounding boxes, instance labels or continuous targets.
- [ ] I can define my class values or object categories.

### Imagery and annotations

- [ ] I know which imagery or raster data will be used.
- [ ] I know where labels or reference data may come from.
- [ ] I have checked whether labels match the imagery date and location.
- [ ] I have visually inspected at least one imagery-label overlay.

### Tiling and pairing

- [ ] I have a first tile size and stride choice.
- [ ] I can explain why overlap is or is not needed.
- [ ] I know how image chips and label chips will be paired.
- [ ] I have considered whether batch processing is needed.

### Quality and splits

- [ ] I have identified likely label-quality problems.
- [ ] I know how I will check class balance or feature counts.
- [ ] I have a first split strategy.
- [ ] I have considered spatial separation between training, validation and test data.
- [ ] I know what parameters must be documented.

---

## 3. Decision points

### What should the model learn?

A reasonable training-data plan begins with the target output. For example, a model might learn building pixels, tree crowns, water areas, crop classes or image-level scene labels.

If you cannot name the target output, the training-data plan is not ready.

### What label format do I need?

Different tasks need different labels.

| Project goal | Likely label format |
| --- | --- |
| Classify whole image chips | Folder or table with one label per chip. |
| Detect objects | Bounding boxes and class labels. |
| Segment pixels | Raster masks. |
| Separate objects | Instance masks or polygons. |
| Predict continuous values | Target raster or point measurements. |

### How will I create labels?

A reasonable label source may be an existing dataset, manual digitising, a public vector layer, a trusted raster product, field observations or a prompted foundation-model workflow.

Every label source needs a quality check.

### What tile size and overlap make sense?

A reasonable tile contains the target feature and enough spatial context. Overlap is useful when objects are often cut by tile boundaries.

A tile setting is not reasonable if it creates huge numbers of files before you have inspected a small prototype.

### How will I split the data?

A reasonable split tests whether the model generalises beyond the exact places it learned from. For spatial data, this usually means region-based, scene-based or buffered splits.

---

## 4. Common pitfalls

- **Waiting for "perfect" annotations before tiling anything.** A small test batch, even with known label gaps, is more useful right now than no batch at all.
- **Choosing a split strategy only after noticing suspiciously good validation results later.** Decide and document your split logic now, while it is still easy to change.
- **Scope creep in what counts as "done" for this lesson.** A complete, well-organized dataset for your full study area is a later milestone; a working, correctly split pipeline on a small sample is the right target today.
- **No documentation of parameters used.** Record tile size, stride, buffer radius, and split ratios somewhere alongside your data, not just in a notebook cell you might overwrite later.

---

## 5. Mini deliverable

Produce a short **Training Data Preparation Plan**, plus a first small batch of tiles if your data is ready:

1. A one-paragraph description of your imagery and annotation sources.
2. Your chosen tile size, stride, and overlap ratio, with a one-sentence justification.
3. Your chosen batch-pairing method, and why it fits your file naming.
4. Your planned split strategy (region-, scene-, or buffer-based), including your target split ratio.
5. If your data is ready: a small folder of generated tiles (even a few dozen), organized into `train/`, `val/`, and `test/` subfolders, with a short note on what you saw when you visually checked a sample.

Keep this plan and this tile folder. You will build directly on them starting in the next task-specific lesson.

---

## 6. Reflection questions

- What is the biggest quality risk in your current annotations, incompleteness, misalignment, or class imbalance, and how will you check for it?
- How confident are you that your planned train/validation/test split avoids spatial leakage? What would make you more confident?
- If your dataset turns out too small once tiled, what is your fallback: a different overlap ratio, additional data sources, or a narrower project scope?
- What tiling and split parameters do you need to document now so that your project remains reproducible later, as covered in the Project handbook?
