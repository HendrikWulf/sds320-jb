---
site:
  outline_maxdepth: 2
---

# Foundation models

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
What pretrained, general-purpose models change about GeoAI work
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. From task-specific to general-purpose

Until recently, most GeoAI models were trained from scratch, or fine-tuned from a general computer-vision backbone, for one specific task on one specific dataset: a model trained to detect buildings would not directly help you detect roads. A {term}`Foundation Model` breaks that pattern — it is trained on very large and diverse data, then adapted to many downstream tasks with comparatively little task-specific data or, in some cases, no additional training at all.

This does not mean foundation models remove the need for careful project design. It shifts where the effort goes: from collecting large labelled datasets and training from scratch, toward choosing the right model, prompting or fine-tuning it well, and critically evaluating its output on your specific area and question.

---

## 2. Two flavours relevant to this book

This book introduces two kinds of foundation models, each covered in a dedicated lesson:

* **Promptable segmentation** — models such as the Segment Anything Model (SAM) can segment objects in an image given a point, box, or text prompt, without being trained for a specific class in advance. Applied to geospatial imagery through packages such as `segment-geospatial`, this is the subject of [L12 – Segment Anything](../12_segment-anything.md).
* **Satellite embedding models** — models trained to produce a compact vector representation ({term}`Satellite Embedding`) of a location or image patch, useful for similarity search, clustering, or as input features to a simpler downstream model. This is the subject of [L13 – Satellite embeddings](../13_satellite-embeddings.md).

```{admonition} Foundation models are a starting point, not a shortcut past evaluation
:class: caution
A foundation model that was never trained on your sensor type, region, or resolution can still perform poorly, sometimes in ways that are not obvious without careful evaluation against ground truth. Treat foundation-model output the same way you would treat any other model prediction: verify it before relying on it.
```

---

## 3. Why this matters for project planning

If your project question maps onto a task a foundation model already supports reasonably well — for example, segmenting a specific type of visible object with SAM — you may need much less {term}`Training Data` than a from-scratch approach would require. If it does not (uncommon classes, subtle spectral distinctions, or fine-grained regression targets), a foundation model may still help as a component, for example generating rough masks that a human then refines, or providing embeddings as features for a simpler classifier trained on your own labels.

Keep this in mind as you develop your project idea: it can be worth checking, early on, whether an existing foundation model already partially solves your problem before planning a full training pipeline.

---

## 4. Key takeaways

* {term}`Foundation Model`s are trained on large, diverse data and adapted to many tasks, rather than trained from scratch for one task.
* This book covers two kinds: promptable segmentation (SAM, [L12](../12_segment-anything.md)) and {term}`Satellite Embedding` models ([L13](../13_satellite-embeddings.md)).
* Foundation models change where project effort goes, but they do not remove the need to evaluate results critically on your own data.
* Checking whether an existing foundation model partially solves your problem is a reasonable early step in project planning.
