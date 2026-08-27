---
site:
  outline_maxdepth: 2
---

# L04 – Training data

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning imagery and annotations into model-ready datasets for GeoAI workflows
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

In the previous lessons, you searched for spatial data, inspected candidate sources and created maps for visual checking. This lesson moves one step closer to modelling: you will prepare {term}`Training Data`.

Training data are the examples a model learns from. In GeoAI, these examples often consist of image chips paired with labels, masks, bounding boxes or continuous target values. A segmentation model might learn from aerial image chips and building masks. An object detector might learn from image chips and bounding boxes. A classifier might learn from image chips and one label per chip.

This lesson focuses on the shared preparation logic behind those workflows. Later method lessons will reuse these ideas for image recognition, object detection, semantic segmentation, instance segmentation, change detection and regression.

---

## 2. Motivation

A model can only learn from the examples it receives. If labels are misaligned, incomplete, outdated or strongly imbalanced, the model will learn those problems too.

Training-data preparation is therefore one of the most important project decisions in GeoAI. You need to check whether imagery and annotations match, convert labels into the right format, split large scenes into manageable chips, inspect the generated pairs and organise the dataset so that training, validation and testing remain meaningful.

This lesson helps you build that inspection habit before the modelling lessons begin.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- explain the full training-data pipeline from imagery and annotations to model-ready files,
- inspect imagery and vector annotations before conversion,
- convert vector polygons into raster masks aligned with a reference image,
- generate paired image-mask chips from a single raster,
- choose tile size, stride and overlap based on project needs,
- run batch workflows for multiple images and annotations,
- identify common label-quality problems and plan spatially meaningful data splits.

---

## 4. Lesson roadmap

Work through the pages in this order:

1. [Training-data pipeline](L04/01_training-data-pipeline.md) — understand the complete workflow from raw imagery to model-ready datasets.
2. [Imagery and annotations](L04/02_imagery-and-annotations.md) — inspect source imagery and labels before creating training data.
3. [Vector to raster masks](L04/03_vector-to-raster-masks.md) — convert polygon annotations into pixel-level masks that match a reference raster.
4. [Tiling and image chips](L04/04_tiling-and-image-chips.md) — split large rasters into manageable chips and preview image-mask pairs.
5. [Batch workflows and pairing](L04/05_batch-workflows-and-pairing.md) — scale the workflow to multiple images and annotation files.
6. [Quality control and splits](L04/06_quality-control-and-splits.md) — check label quality, class balance, edge effects and train/validation/test splits.
7. [Project transfer](L04/07_project-transfer.md) — design a training-data plan for your own SDS320 project.

---

## 5. Project framing

As you work through this lesson, keep your own project in mind.

Use these guiding questions:

- What would count as one training example in my project?
- Do I need labels, masks, bounding boxes, instance IDs or continuous target values?
- Can I visually check that labels align with the imagery?
- How will I avoid training and validation data that are spatially too similar?

```{tip}
A small, carefully checked training dataset is usually more useful than a large dataset whose labels you have not inspected.
```

---

## 6. Before class

Before class, prepare the following:

- [ ] Choose one possible imagery dataset from Lesson 02.
- [ ] Identify whether you already have labels, annotations or reference data.
- [ ] Bring one small area where you can inspect imagery and labels together.

---

## 7. After this lesson

After completing this lesson, you should have:

- a first training-data pipeline sketch for your project,
- a decision about which labels or reference data you need,
- at least one visual check of imagery and annotations,
- a first idea for tile size, stride and overlap,
- a plan for organising training, validation and test data,
- a list of label-quality risks to check before modelling.

This does not mean your training data must be final. It means you should know what needs to be created, inspected and documented before training a model.
