---
site:
  outline_maxdepth: 2
---

# L08 – Instance Segmentation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Delineating individual objects into measurable spatial features
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

In L06 you trained an {term}`object detection <Object Detection>` model that draws a box around each discrete object. In L07 you trained a {term}`semantic segmentation <Semantic Segmentation>` model that labels every pixel with a class, but treats all pixels of the same class as one undifferentiated region. This lesson combines both ideas: instance segmentation detects each object individually *and* traces its precise pixel-level boundary, so two neighboring fields, buildings, or tree crowns each get their own separate mask, even though they share the same class.

This is the last of the three "detect and delineate" lessons in the course. Everything you learned about bounding boxes and confidence scores in L06, and about encoder-decoder masks in L07, comes together directly in {term}`Mask R-CNN`, the architecture this lesson uses.

---

## 2. Motivation

Many geospatial questions genuinely need object-level answers, not just class-level ones. Agricultural analysts need individual field parcels to estimate yield or verify subsidy claims; the European Union's Common Agricultural Policy, for example, requires accurate per-field information across millions of parcels, at a scale where manual digitization is not realistic. Urban planners need individual building footprints, not just a "built-up" class. Disaster response teams need to count and locate individual damaged structures, not just a "damage" region.

In each case, semantic segmentation alone would under-deliver: it can tell you "this area is field," but not how many fields, where each one starts and ends, or how its shape has changed since last season. Instance segmentation is what turns a thematic class map into a countable, measurable, trackable set of discrete features, ready to be joined, filtered, and analyzed as records in a GIS. In simple terms, {term}`instance segmentation <Instance Segmentation>` is useful when an object's **boundary and identity both matter**.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- Decide whether a project question needs instance-level separation, or whether detection or semantic segmentation alone is sufficient
- Explain how {term}`Mask R-CNN` extends Faster R-CNN with a mask prediction branch, and why RoI Align matters for boundary precision
- Prepare a benchmark instance segmentation dataset and understand its expected data structure
- Train a Mask R-CNN model with `geoai` and interpret its multi-part loss during training
- Run sliding-window inference and interpret the resulting instance, class, and score rasters
- Clean, vectorize, and compute geometric properties for instance predictions, and scale the workflow to batches of images

---

## 4. Lesson roadmap

1. [When instance segmentation fits](L08/01_when-to-use-instance-segmentation.md) — how this task differs from detection and semantic segmentation, and when the extra complexity is worth it.
2. [Instances and object identity](L08/02_instances-masks-and-object-identity.md) — instance IDs, per-object masks, and how instance labels differ from class labels.
3. [Mask R-CNN architecture](L08/03_mask-rcnn-architecture.md) — backbone, Region Proposal Network, detection head, mask head, and why RoI Align matters.
4. [The FTW dataset](L08/04_ftw-dataset-and-preparation.md) — the Fields of the World benchmark, its Sentinel-2 chips, and preparing data for training.
5. [Training Mask R-CNN](L08/05_training-a-mask-rcnn-model.md) — the main hands-on workflow: training a field-boundary detector with `geoai`.
6. [Inference and raw predictions](L08/06_inference-and-raw-predictions.md) — sliding-window inference and reading the instance, class, and score outputs.
7. [Cleaning and vectorizing](L08/07_cleaning-vectorisation-and-properties.md) — post-processing raw masks into clean, GIS-ready polygons with geometric properties.
8. [Batch processing & project transfer](L08/08_batch-processing-and-project-transfer.md) — scaling inference to many images, and applying this lesson to your own project.

Pages 1–2 build the conceptual foundation, page 3 covers the architecture, page 4 prepares the data, page 5 is the main training workflow, and pages 6–7 apply and refine the model's output. Page 8 scales up and transfers everything to your project.

---

## 5. Project framing

While reading this lesson, keep your own project in mind and consider:

- Do you need to count, measure, or track individual objects, or would a class map (L07) or a set of bounding boxes (L06) already answer your question?
- What is a realistic object size for your target features relative to your imagery's resolution?
- Do you have, or can you obtain, instance-level labels (separate masks per object), or only class-level labels?
- What geometric properties (area, shape, elongation) would your project actually need to compute once objects are detected?

---

## 6. Before class

- Review the L06 object-detection lesson, especially bounding boxes, confidence scores, and non-maximum suppression, all of which reappear here.
- Review the L07 semantic-segmentation lesson, especially masks and encoder-decoder architectures.
- Make sure `geoai` is installed and importable in your environment.
- Think of one type of discrete, individually meaningful object in your own project area (a field, a building, a tree crown, or similar).

---

## 7. After this lesson

After working through this lesson, you should have:

- A clear decision on whether instance segmentation fits your project, or whether a simpler task (detection or semantic segmentation) is sufficient
- A working understanding of the full Mask R-CNN pipeline, from raw imagery to cleaned, GIS-ready polygons
- A first sketch of your own instance-segmentation classes, data source, and post-processing plan, to be developed further on the final page
