---
site:
  outline_maxdepth: 1
---

# Training-data pipeline

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Understanding the path from raw spatial data to model-ready examples
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this pipeline matters

Every task-specific lesson later in this course, image recognition, object detection, segmentation, will assume you already know how to get from "an image and some labels" to "a folder of model-ready tiles." Having the whole pipeline in view before diving into any one step helps you understand why each step exists, rather than treating it as an arbitrary sequence of function calls.

In any deep learning project, data preparation and splitting are the first steps of a longer checklist that also includes model architecture, hyperparameters, training, and evaluation. Seeing where this lesson fits in that bigger picture can help you plan your own project's timeline realistically.

---

## 2. Core idea

Regardless of the specific task, GeoAI training data is built through the same sequence of transformations: acquire, inspect, convert, tile, pair, validate, split. The tools differ slightly by task, but the sequence does not.

A common pipeline is:

```text
acquire imagery and labels
→ inspect imagery and annotations
→ convert labels if needed
→ tile imagery
→ generate paired label tiles
→ visualise and validate examples
→ split into training, validation and test sets
→ document the dataset
```

This pattern appears again and again in GeoAI. The exact output format changes by task, but the quality checks are similar.

| Task type | Typical input | Typical label |
| --- | --- | --- |
| {term}`Image Recognition` | Image chip | One class label per chip |
| {term}`Object Detection` | Image chip | Bounding boxes and class labels |
| {term}`Semantic Segmentation` | Image chip | Pixel-level raster mask |
| {term}`Instance Segmentation` | Image chip | Separate object masks or instance IDs |
| {term}`Pixel-Level Regression` | Image chip | Continuous target raster |

The rest of this lesson focuses mainly on image-mask workflows because they are a strong foundation for segmentation and related tasks.

---

## 3. Workflow overview

### A. Acquire imagery and labels

Start from imagery and annotations you have already found and checked in [L02 – Data acquisition](../02_data-acquisition.md) and [L03 – Interactive mapping and visualization](../03_interactive-mapping-visualisation.md). {term}`Annotations <Annotation>` might be building footprints, land cover polygons, or bounding boxes, depending on your task. General machine learning guidance frames this stage as data collection followed by cleaning: correcting missing values, inconsistent formats, and obvious errors before anything else happens.

### B. Inspect before conversion

Before creating chips, inspect imagery and labels together. This catches obvious problems such as wrong location, misaligned labels, unexpected clouds, incomplete annotations or outdated reference data.

### C. Convert vector labels to raster masks

Some models need labels in a different format from the data you start with. For example, {term}`semantic segmentation` models need a {term}`Raster Mask`. So, {term}`vector data <Vector Data>` such as polygons has to be rasterized onto a grid that matches the imagery's resolution, extent, and {term}`CRS <Coordinate Reference System (CRS)>`. You will do this on the [vector-to-raster page](03_vector-to-raster-masks.md).

### D. Tile the imagery

A satellite scene can be tens of thousands of pixels across, while most models expect inputs of a few hundred pixels. Tiling slices the scene into fixed-size {term}`chips <Chip>` that actually fit into GPU memory and match your model's expected input size. Covered on the [tiling page](04_tiling-and-image-chips.md).

### E. Generate paired labels

Each image chip needs a matching label chip. If an image tile covers a particular spatial extent, its mask tile must cover exactly the same extent.

### F. Visualize and validate

Before training anything, inspect the generated tiles to confirm that labels line up with real features in the imagery. Skipping this step is one of the most common sources of wasted training time.

### G. Split into subsets

Divide the dataset into training, validation, and test sets, with enough spatial separation between them to avoid data leakage. Covered on the [quality control and splits page](06_quality-control-and-splits.md).

```{admonition} The pipeline does not change with the task
:class: note
Whether you are eventually building a building-segmentation model, a crop classifier, or a change-detection model, this six-step sequence stays the same. Only the specific labels (masks, boxes, class rasters) and some parameter choices change.
```

---

## 4. Key takeaways

- Training data connect raw spatial data to model learning.
- Different GeoAI tasks need different label formats.
- Imagery and annotations should be inspected before conversion.
- Image chips and label chips must match spatially.
- Spatial splitting matters for honest evaluation.

---

## 5. Further reading

- [What is Training Data? (IBM)](https://www.ibm.com/think/topics/training-data) — a general, non-geospatial overview of why training data quality matters and how a typical data pipeline (collection, cleaning, splitting, labeling) is organized.
- [Deep Learning Model Training Checklist (OpenCV)](https://opencv.org/deep-learning-model-training/) — shows where data preparation and splitting sit within the full deep learning workflow, from data validation through deployment.
