---
site:
  outline_maxdepth: 2
---

# Embedding concepts

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
What an embedding actually is, and the formats it comes in
</div>
<!-- markdownlint-enable MD033 -->

---

You now know where embeddings fit. This page explains what one actually is, which will make every later page in this lesson, the registry, the Clay workflow, clustering, similarity search, feel like natural consequences of this one idea rather than a collection of separate techniques.

---

## 1. Motivation

Every function in this lesson operates on embeddings as plain numerical vectors. Understanding what those numbers represent, and just as importantly, what they do not represent, is what will keep you from over-interpreting a similarity score or a cluster assignment later in this lesson.

---

## 2. Core idea

An **{term}`embedding <Embedding>`** is a fixed-length numerical vector produced by a foundation model that summarizes the content of an image patch or pixel location. It is not a prediction or a final answer; it is a compact feature representation, an intermediate summary you build further analysis on top of. The geometry of the space these vectors live in is what makes them useful: two locations with similar land cover end up with embeddings that are close together, while very different locations end up far apart.

---

## 3. What an embedding is, and is not

### A. A vector, not an answer

An embedding for one image patch might be a list of 768 numbers (Clay) or 128 numbers (TESSERA). None of those individual numbers has an obvious, human-readable meaning on its own, the way a class label or a confidence score does. What matters is the vector's *position* relative to other vectors, not any single dimension's value in isolation.

```{admonition} Model output vs. feature representation
:class: important
A segmentation model's output is a direct answer: this pixel is "building." An embedding is not that. It is a feature representation, a compressed description of what the model "saw," left deliberately open-ended so that many different downstream questions (similarity, clustering, classification) can be asked of the same vector without recomputing anything. Keep this distinction in mind whenever you are tempted to read an embedding as if it were already an answer.
```

### B. Why pre-training without labels works

Foundation models that produce embeddings are typically trained through **{term}`self-supervised learning <Self-Supervised Learning>`**: instead of learning from human-provided labels, the model learns by solving a task it can generate from the raw data itself, for example predicting a masked-out part of an image from the rest of it. This is what lets these models train on vast amounts of unlabeled satellite imagery, and it is also why the resulting embeddings generalize reasonably well to imagery and locations the model was never specifically shown, the same generalization property that made Segment Anything useful in L12, here applied to producing reusable features rather than direct segmentations.

### C. Patch-based and pixel-based formats

Embeddings come in two formats, and this lesson uses both:

- **Patch-based** embeddings summarize an entire image tile, typically several square kilometers, into a single vector. They are stored as {term}`GeoParquet` files, one row per tile, each row holding an embedding vector plus metadata like coordinates and timestamp. Patch-based embeddings suit scene-level tasks: image retrieval, land-use classification, regional comparison. Clay, used in this lesson's main workflow, is patch-based.
- **Pixel-based** embeddings assign a vector to every pixel in a raster grid, stored as multi-band GeoTIFF files where each band is one embedding dimension. Pixel-based embeddings support finer-grained spatial work: segmentation-adjacent analysis, per-pixel classification, local change detection. TESSERA, covered later in this lesson, is pixel-based.

Choosing between them is mostly a question of scale: patch-based embeddings are compact and efficient for large-area, scene-level questions, while pixel-based embeddings cost more to store and process but support analysis at the resolution of individual pixels.

---

## 4. Python reactivation

A single embedding is a one-dimensional NumPy array, the same object type you have used for raster band values throughout this course, just not spatially arranged. Stacking many embeddings together (one per patch or pixel) produces a two-dimensional array, rows for locations and columns for embedding dimensions, the exact shape `scikit-learn` functions expect, which is why embeddings slot so directly into standard machine learning tooling once extracted.

---

## 5. Common pitfalls

- **Treating an embedding as a direct prediction.** It is a feature representation; you still need a downstream step (similarity, clustering, classification) to turn it into an answer.
- **Confusing embedding dimension with spatial resolution.** A 768-dimensional embedding does not mean 768 meters or 768 pixels; dimension count and spatial scale are independent properties of a dataset.
- **Interpreting individual embedding values in isolation.** A single dimension's number rarely means anything on its own; relative position and distance between vectors is what carries meaning.
- **Assuming patch-based and pixel-based embeddings are interchangeable.** They suit different scales of question; check which format your specific analysis actually needs before choosing a dataset, the focus of the next page.

---

## 6. Mini task

A colleague says, "an embedding is basically the same thing as a classification prediction, just with more numbers." Explain what this gets wrong, using the ideas from this page.

:::{dropdown} Sample solution
:class: note

A classification prediction is a direct answer to a specific question (what class is this). An embedding is not an answer to any particular question at all; it is a general-purpose feature representation that can be used to answer many different downstream questions, similarity, clustering, classification with a separate lightweight model, without being recomputed. Having "more numbers" is not the key difference; the key difference is that an embedding is an intermediate representation, not a finished prediction.
:::

---

## 7. Key takeaways

- An embedding is a fixed-length vector summarizing a patch or pixel's content; it is a feature representation, not a direct prediction.
- Geometric closeness in embedding space corresponds to semantic similarity between locations.
- Self-supervised pre-training lets foundation models learn from unlabeled imagery, producing embeddings that generalize to unfamiliar locations.
- Patch-based embeddings (GeoParquet, one vector per tile) suit scene-level questions; pixel-based embeddings (GeoTIFF, one vector per pixel) suit fine-grained spatial questions.

### Further reading

- [ISPRS 2026 Embeddings Tutorial](https://github.com/konstantinklemmer/isprs26-embeddings-tutorial/blob/main/README.md) — a broader tutorial on satellite embedding concepts and workflows beyond this lesson's scope.
