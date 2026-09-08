---
site:
  outline_maxdepth: 2
---

# When to use embeddings

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Recognising when a pre-computed representation beats training a model from scratch
</div>
<!-- markdownlint-enable MD033 -->

---

The landing page introduced embeddings as a reusable numerical summary of a place. This page works through where that summary is genuinely useful, and where the pixel-precise, trained-model approach from earlier lessons remains the better fit.

---

## 1. Motivation

Embeddings are appealing partly because they are cheap: no training, sometimes only a handful of labels. That cheapness is a real advantage for some questions and beside the point for others. Knowing which is which before you start saves you from either underusing a fast tool or over-relying on one where it does not fit.

---

## 2. Core idea

Embedding-based workflows fit questions about similarity, grouping, and lightweight classification: which places look alike, what natural groupings exist in a landscape, or which of a few known categories a location belongs to. They fit less well when a project needs precise, pixel-level boundaries or calibrated physical measurements, the territory of the {term}`foundation model <Foundation Model>`-adjacent but task-specific methods from L06 through L11.

---

## 3. Where embeddings tend to work well

### A. Exploratory analysis

If you want to understand a study area before committing to a specific method, projecting its embeddings and looking at their structure (covered two pages from now) is a fast way to get oriented. Similarity search directly answers "where else looks like this," useful for disaster response, ecological monitoring, or urban planning comparisons, without writing a task-specific model at all.

### B. Low-label classification

Because a foundation model has already learned rich features during pre-training, a simple classifier trained on embeddings can perform well with only a small number of labeled examples, sometimes fewer than a hundred points. If your project has limited labeling capacity, this is a genuine advantage over training a segmentation or detection model from scratch.

### C. Clustering, comparison, and CD

Unsupervised clustering on embeddings reveals natural groupings, land cover types, urban patterns, without any labels at all. Comparing embeddings for the same location at different times offers a lightweight route to change detection, an alternative to the ChangeStar workflow from L10 when you do not need per-pixel change maps, only a signal of where change is likely.

### D. Choosing a task-specific model

If your project needs a precise object boundary, an exact building footprint, a field parcel outline, embeddings alone do not provide that; you would still want segmentation (L07 or L08). If your project needs a calibrated, physically meaningful measurement, a specific NDVI value, a height in meters, regression (L11) trained on your specific target remains the right tool. Embeddings answer "how similar" and "what category," not "what is the precise shape" or "what is the exact value."

```{admonition} A quick mental test
:class: tip
Ask: "Am I asking how similar two places are, or what small number of categories a location falls into?" If yes, embeddings are a strong candidate. If you need an exact boundary or a calibrated physical value, plan for the task-specific methods from earlier lessons instead.
```

---

## 4. Python reactivation

No new Python here. This page is a decision framework; the hands-on workflow begins two pages from now, once the next page explains what an embedding actually is.

---

## 5. Common pitfalls

- **Expecting pixel-precise boundaries from an embedding-based workflow.** Embeddings summarize a patch or pixel's overall character; they do not trace an exact outline the way a segmentation model does.
- **Treating a small labeled validation set as sufficient proof of accuracy.** A classifier trained and validated on a handful of points still deserves the same skepticism as any other model's evaluation.
- **Assuming any embedding dataset covers your study area at a useful resolution.** Coverage, resolution, and temporal range vary across datasets, covered directly on the registry page ahead; check before committing to one.
- **Reaching for embeddings out of convenience when the project genuinely needs calibrated measurement or precise geometry.** Convenience is not the same as fit; revisit the mental test above when in doubt.

---

## 6. Mini task

Your project has two goals: (a) get a rough sense of how many distinct landscape types exist across a large, unfamiliar study area, and (b) measure the exact area of wetland loss over five years for a regulatory report. Which goal fits an embedding-based approach, and which needs a task-specific method from an earlier lesson?

:::{dropdown} Sample solution
:class: note

Goal (a) fits embeddings well: unsupervised clustering on embeddings is exactly suited to discovering natural groupings in an unfamiliar landscape without any labels. Goal (b) needs a task-specific method, most likely semantic segmentation (L07) trained or evaluated specifically for wetland boundaries, since a regulatory report needs a precise, defensible area measurement that a similarity- or cluster-based embedding signal cannot provide on its own.
:::

---

## 7. Key takeaways

- Embedding-based workflows fit questions about similarity, grouping, and lightweight classification with few labels.
- Precise boundaries and calibrated physical measurements still call for the task-specific methods from earlier lessons.
- Low-label classification is a genuine advantage of embeddings for projects with limited labeling capacity.
- The next page explains what an embedding actually is, which will make the rest of this lesson's workflow easier to reason about.

### Further reading

- OpenGeoAI, ["Foundation Models"](https://opengeoai.org/examples/foundation_models/) — a broader overview of foundation-model workflows in `geoai`, including but not limited to embeddings.
