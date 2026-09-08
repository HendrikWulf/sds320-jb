# L13 - Satellite embeddings

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Numerical representations of imagery for similarity, clustering and lightweight classification
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

This is the final method lesson in SDS320, and it closes the course with a different kind of shortcut than L12's. Segment Anything let you skip training by prompting a {term}`foundation model <Foundation Model>` directly for a specific task. Satellite embeddings let you skip training by reusing a foundation model's internal representation of a place, a compact numerical summary you compute once and then use for similarity search, clustering, classification, or change detection, largely as fast, lightweight operations on vectors rather than new deep learning pipelines.

Everything from L05 through L12 asked a foundation model, or a trained model, to answer a specific question directly: what class, what box, what mask, what change. This lesson asks a different question first: what does this place *look like*, numerically, and lets you answer many downstream questions cheaply once you have that answer.

---

## 2. Motivation

Every earlier method lesson in this course involved some form of task-specific effort: labeling, training, or at least careful prompting. Embeddings offer a genuinely different economy. A handful of labeled points, sometimes fewer than a hundred, can train a working classifier on top of a pre-computed embedding, because the foundation model has already done the expensive work of learning what matters visually and spectrally about a landscape.

For a course project with limited time and limited labels, this matters directly. If precomputed embeddings already exist for your study area, you may be able to explore, cluster, and even classify your data without training anything from scratch, or committing to a specific architecture at all.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- Decide when an embedding-based workflow fits a project, and when a task-specific trained model is still the better choice
- Explain what an embedding vector represents, and how patch-based and pixel-based embeddings differ
- Browse and query `geoai`'s embedding dataset registry to find a dataset suited to your analysis
- Download and work with Clay Foundation Model embeddings stored as GeoParquet
- Visualize embedding structure with PCA, discover patterns with clustering, and retrieve similar locations with similarity search
- Train a lightweight classifier on embeddings using a small number of labeled points
- Work with pixel-based TESSERA embeddings and cloud-based AlphaEarth embeddings in Google Earth Engine

---

## 4. Lesson roadmap

1. [When embeddings fit](L13/01_when-to-use-satellite-embeddings.md) — typical use cases, and when a task-specific model is still the better choice.
2. [Embedding concepts](L13/02_embedding-concepts-and-formats.md) — what an embedding vector is, why similar places end up close together, and patch- versus pixel-based formats.
3. [The embedding registry](L13/03_embedding-datasets-and-registry.md) — browsing and filtering `geoai`'s catalog of available embedding datasets.
4. [Clay embeddings](L13/04_patch-based-embeddings-with-clay.md) — the main hands-on workflow: downloading and loading patch-based Clay Foundation Model embeddings.
5. [Visualizing & searching](L13/05_visualisation-clustering-and-similarity.md) — PCA visualization, K-means clustering, and cosine similarity search.
6. [Classification & change](L13/06_lightweight-classification-and-change.md) — training lightweight classifiers and comparing embeddings for change detection.
7. [Pixel & cloud embeddings](L13/07_pixel-based-and-cloud-embeddings.md) — TESSERA pixel-based embeddings and AlphaEarth in Google Earth Engine.
8. [Project transfer](L13/08_project-transfer.md) — deciding whether embeddings can support your project, and planning your analysis.

Pages 1–2 build the conceptual foundation, page 3 helps you find the right dataset, page 4 is the main hands-on workflow, and pages 5–7 extend it with unsupervised exploration, classification, and alternative embedding types. Page 8 transfers everything to your project.

---

## 5. Project framing

While reading this lesson, keep your own project in mind and consider:

- Would knowing "what does this place look like, numerically" already help answer part of your project question?
- Do you have, or could you quickly create, a small number of labeled points rather than a full training dataset?
- Is your study area likely covered by an existing embedding dataset, and at a resolution that matches your needs?
- Does your project need pixel-precise boundaries (better suited to earlier lessons), or would a similarity, cluster, or lightweight classification answer be enough?

---

## 6. Before class

- Review the L12 Segment Anything lesson, since both lessons share the "foundation model, minimal task-specific effort" theme.
- Make sure `geoai` is installed and importable, and that you have (or can create) a Hugging Face account for downloading Clay embeddings.
- If you plan to explore the AlphaEarth section, create a free Google Earth Engine account ahead of time.
- Think of one question in your own project that might be answerable through similarity or a handful of labeled examples, rather than a fully trained model.

---

## 7. After this lesson

After working through this lesson, and this course, you should have:

- A clear decision on whether embeddings can support your project, alongside or instead of the trained and prompted models from earlier lessons
- A working understanding of the full embedding workflow: finding a dataset, loading it, visualizing and clustering it, classifying with minimal labels, and comparing it across time
- A first sketch of your own embedding-based analysis plan, to be developed into concrete project work on the final page
