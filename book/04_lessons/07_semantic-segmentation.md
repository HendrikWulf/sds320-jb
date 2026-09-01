---
site:
  outline_maxdepth: 2
---

# L07 – Semantic Segmentation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning geospatial imagery into pixel-level thematic maps
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

In L04 you prepared {term}`training data <Training Data>` for GeoAI workflows. In L06 you trained an {term}`object detection <Object Detection>` model that draws a box around each discrete object it finds. This lesson takes a different approach to the same broad problem: rather than localizing individual objects, a semantic segmentation model assigns a class label to *every pixel* in an image, producing a dense {term}`mask <Mask>` at the same spatial dimensions as the input.

This distinction matters because many geospatial questions are not about discrete, countable objects at all. Mapping a water body, a forest, a cloud, or a land-cover class is fundamentally a "which pixels belong to which class" question, not a "how many objects and where" question. Semantic segmentation is the right tool for exactly this kind of continuous, area-based mapping.

Looking ahead, L08 combines the ideas from this lesson with the object-level awareness of L06 into instance segmentation, which separates individual objects while still tracing their pixel-level boundaries. Everything you learn here about masks, encoders, and decoders carries forward directly.

---

## 2. Motivation

A large share of practical remote sensing work is really segmentation work: water masks for flood monitoring, cloud masks as a preprocessing step before almost any other analysis, building footprints for urban planning, and land-cover maps for environmental and agricultural monitoring. In each case, the deliverable is a thematic map, not a list of objects.

Segmentation also tends to be more forgiving of irregular shapes than detection. A river, a coastline, or a field boundary rarely fits neatly into a rectangle, so a pixel-level prediction captures the actual geometry in a way a bounding box cannot. For a project that needs area estimates, boundary delineation, or a full thematic map, segmentation is usually the more direct path than trying to force the problem into a detection or classification framing.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- Decide whether a project question calls for semantic segmentation rather than classification or detection
- Explain image-to-mask prediction, the encoder-decoder pattern, and the role of skip connections
- Compare common segmentation architectures and encoders, and choose a sensible starting point for a project
- Train a binary segmentation model with `geoai`, evaluate it with IoU and F1, and run inference on new imagery
- Adapt a segmentation workflow across different band configurations (RGB, multispectral, aerial) and understand what changes and what stays the same
- Judge when a pre-trained, sensor-agnostic model is an appropriate shortcut, and what still needs checking when you use one
- Extend a workflow to multi-class land-cover segmentation and convert raster predictions into GIS-ready vector outputs

---

## 4. Lesson roadmap

1. [When segmentation fits](L07/01_when-to-use-semantic-segmentation.md) — how segmentation differs from classification and detection, and when pixel-level masks are the right choice.
2. [Segmentation concepts](L07/02_pixel-masks-and-segmentation-concepts.md) — image-to-mask prediction, binary and multi-class masks, probability maps, and sliding-window inference.
3. [Architectures and encoders](L07/03_segmentation-architectures-and-encoders.md) — the encoder-decoder pattern, skip connections, U-Net as a baseline, and when to consider alternatives.
4. [Binary segmentation workflow](L07/04_binary-segmentation-workflow.md) — the main hands-on workflow: building footprint segmentation from data preparation through evaluation and vector output.
5. [Bands and sensors](L07/05_multispectral-and-sensor-specific-workflows.md) — comparing RGB, Sentinel-2, and NAIP inputs for the same task, and how band configuration affects results.
6. [Pre-trained models](L07/06_pretrained-segmentation-models.md) — using sensor-agnostic, inference-only models for water and cloud masking, and what responsibility remains yours.
7. [Multi-class and post-processing](L07/07_multiclass-segmentation-and-postprocessing.md) — land-cover classification with many classes, and turning raster predictions into clean vector outputs.
8. [Project transfer](L07/08_project-transfer.md) — deciding whether segmentation fits your project, and sketching your classes, bands, and evaluation plan.

Pages 1–3 build the conceptual foundation. Page 4 is the main hands-on workflow, and pages 5–7 extend it across sensors, pre-trained models, and multi-class problems. Page 8 transfers everything to your own project.

---

## 5. Project framing

While reading this lesson, keep your own project in mind and consider:

- Is your project's deliverable a thematic map (a class per pixel) or a set of individual objects (better suited to L06)?
- What classes matter for your project, and are they visually or spectrally distinct enough to separate?
- Which bands does your available imagery provide, and would additional spectral information (like NIR or SWIR) plausibly help?
- Do you have, or can you create, pixel-level labels for your classes, or would you rather start from a pre-trained model?

---

## 6. Before class

- Review the L04 training-data lesson, especially tiling and label rasterization, since this lesson builds directly on those steps.
- Skim the L06 object-detection workflow so the general `geoai` training/evaluation pattern feels familiar.
- Make sure `geoai` is installed and importable in your environment.
- Think of one class in your own project area that could plausibly be mapped as a mask (water, buildings, vegetation, or similar).

---

## 7. After this lesson

After working through this lesson, you should have:

- A clear decision on whether semantic segmentation fits your project, or whether detection (L06) or instance segmentation (L08) is a better match
- A working understanding of how band configuration, architecture choice, and class count affect a segmentation workflow
- A first sketch of your own segmentation classes, input bands, and evaluation plan, to be developed further on the project-transfer page
