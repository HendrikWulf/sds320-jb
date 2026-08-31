---
site:
  outline_maxdepth: 2
---

# When to use detection

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Match the computer-vision task to the spatial question before choosing a model
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this decision matters

Picking the wrong vision task for a project question wastes time. If you train a classifier when you actually need object counts, or a segmentation model when a handful of bounding boxes would answer your question, you will spend weeks on data preparation and training before discovering the mismatch. Making this decision early, and revisiting it explicitly on the project-transfer page, keeps your SDS320 project scoped realistically.

---

## 2. Core idea

The three tasks answer different questions about the same image:

- {term}`Image recognition <Image Recognition>` answers "what is this scene, overall?" It returns one label (or a small set of labels) for the whole {term}`chip <Chip>`. It says nothing about location or count.
- {term}`Object detection <Object Detection>` answers "what discrete objects are present, and roughly where?" It returns a variable number of {term}`bounding boxes <Bounding Box>`, each with a class label and a {term}`confidence score <Confidence Score>`. It localizes objects without tracing their exact outline.
- {term}`Semantic segmentation <Semantic Segmentation>` answers "which class does each pixel belong to?" It produces a full thematic map, but it cannot separate two touching objects of the same class, for example two cars parked side by side both simply become "car" pixels.

Object detection is the right choice when your project question is fundamentally about discrete, countable things: how many, and approximately where. It is not the right choice when you need exact object boundaries, for example measuring a building footprint's area or delineating a field parcel precisely. In that case, {term}`instance segmentation <Instance Segmentation>` (L08), which combines detection-style object separation with pixel-level masks, is usually the better fit.

```{admonition} A quick mental test
:class: tip
Ask yourself: "If I had perfect bounding boxes for every object, would that answer my project question?" If yes, detection is a strong candidate. If you would still need the precise shape of each object, look ahead to segmentation or instance segmentation instead.
```

---

## 3. Typical geospatial use cases

### A. Counting and inventorying

Trees in a park, ships in a harbor, solar panels on rooftops, or wind turbines across a region are all use cases where the underlying question is a count, possibly broken down by class or location. Detection outputs convert directly into point or polygon features you can aggregate in a GIS.

### B. Locating rare or specific features

Sports facilities (tennis courts, baseball diamonds), bridges, or storage tanks are often sparse in an image but important to find. A detector trained on a handful of classes can scan large areas of imagery and flag candidate locations for review, which is far faster than manual visual inspection.

### C. When detection is not enough

If your question depends on exact shape or area, for example "what fraction of this rooftop is covered by solar panels" or "what is the footprint area of this building," a bounding box only gives you an approximate rectangle around the object. You would need segmentation or instance segmentation to get a usable boundary.

#### Prefer classification for scene-level questions

Suppose your question is:

> Which image tiles contain industrial land use?

You may not need individual objects at all. A classification workflow could be easier to train, annotate and evaluate.

#### Prefer segmentation for continuous features

Consider:

> Which pixels belong to water?

Water bodies are often continuous spatial features rather than collections of separate objects. Pixel-level segmentation usually represents this question better.

#### Prefer instance segmentation when boundaries matter

Suppose you want the area of every detected building.

A rectangular box contains background pixels and does not reproduce the building footprint. Instance segmentation is more appropriate when individual identities **and** precise boundaries are important.

---

## 5. Common pitfalls

- **Choosing detection because the data already has points, not boxes.** Point locations are not the same as detection annotations. You will still need to draw or generate bounding boxes around each object before training.
- **Underestimating object size relative to resolution.** If your target objects span only a few pixels in your available imagery, detection performance may be poor regardless of architecture. Check your {term}`spatial resolution <Spatial Resolution>` against your object size early.
- **Assuming detection gives you area or shape.** A bounding box is a rectangle, not a boundary. If your deliverable is an area estimate, revisit whether segmentation is more appropriate.

---

## 6. Mini task

Look at your own project's target objects (or, if you do not have one yet, pick one of: individual trees, greenhouses, or agricultural storage buildings). Write two or three sentences answering:

1. Is your goal best described as "classify the scene," "find and count discrete objects," or "map exact object boundaries or continuous surfaces"?
2. Based on that answer, would classification, detection, or segmentation fit best?

:::{dropdown} Sample solution
:class: note

For "greenhouses in an agricultural region": the goal is to locate and count individual greenhouse structures, which are discrete, separable objects with fairly regular rectangular shapes. This points toward object detection. If the project instead needed the exact covered area per greenhouse (for example, to estimate total covered cultivation area), instance segmentation would be a better match because it would trace each greenhouse's outline rather than only its bounding rectangle.
:::

---

## 7. Key takeaways

- Image recognition labels a whole scene, object detection localizes discrete objects with boxes, and semantic segmentation labels every pixel without separating instances.
- Detection is appropriate for counting, locating, and mapping discrete objects; it is not appropriate when you need exact object boundaries.
- Object size relative to image resolution is a practical constraint to check before committing to detection.
- The next page introduces the concepts, bounding boxes, confidence scores, IoU, non-maximum suppression, and anchor boxes, that make detection models work.
