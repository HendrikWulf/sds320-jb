# L12 - Segment Anything

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Segmenting almost any object with a prompt, no task-specific training needed
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

L07 and L08 trained {term}`semantic segmentation <Semantic Segmentation>` and {term}`instance segmentation <Instance Segmentation>` models from labeled data you prepared yourself. This lesson introduces a different starting point: a {term}`foundation model <Foundation Model>`, the {term}`Segment Anything Model (SAM)`, pre-trained on over a billion masks, that segments objects it has never specifically been trained on, with no task-specific training required from you at all.

This does not make earlier lessons obsolete. It shifts where your effort goes. Instead of spending time on labeling and training, you spend it on prompt design and quality control, deciding what to ask the model for, and how much to trust what it returns.

---

## 2. Motivation

Building a training dataset for something like building footprint extraction in a single city already takes real effort: digitizing hundreds of outlines, keeping annotators consistent, then hoping the model generalizes to a different city with different roof materials and imaging conditions. A foundation model changes this calculus. The same pre-trained model can plausibly delineate buildings in Nairobi, fields in Iowa, and rivers in the Amazon without a single labeled example from any of those places.

For a project with limited time, this means initial segmentation results in minutes rather than weeks. It does not remove the need for domain judgment or quality control, a theme this entire lesson returns to repeatedly, but it dramatically lowers the cost of getting a first result to evaluate.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- Decide when a zero-shot, foundation-model workflow fits a project, and when a task-specific trained model is still the better choice
- Explain SAM's image encoder, prompt encoder, and mask decoder at a conceptual level, and why encoding an image once matters
- Set up `samgeo`'s `SamGeo3` class for georeferenced imagery, including confidence scores and vector output
- Segment imagery using text, point, and box prompts, and choose the right one for a given task
- Extract and vectorize building footprints, including polygon regularization for GIS-ready output
- Scale a segmentation workflow across multiple images and large rasters, and track objects across video frames

---

## 4. Lesson roadmap

1. [When Segment Anything fits](L12/01_when-to-use-segment-anything.md) — where zero-shot segmentation is genuinely useful, and where a trained model still wins.
2. [SAM concepts & prompts](L12/02_sam-concepts-and-prompts.md) — the image encoder, prompt encoder, mask decoder, and the "encode once, prompt cheaply" workflow.
3. [samgeo setup & outputs](L12/03_samgeo-setup-and-geospatial-outputs.md) — bridging SAM to georeferenced data: setup, access, and geospatial output formats.
4. [Text, point & box prompts](L12/04_text-point-and-box-prompts.md) — the three main prompt types, and when each one fits best.
5. [Building extraction](L12/05_building-extraction-and-vectorisation.md) — the main hands-on workflow: point and box prompts, vectorization, and polygon regularization.
6. [Batch & tiled segmentation](L12/06_batch-and-tiled-segmentation.md) — scaling to multiple images and rasters too large for memory.
7. [Interactive & video segmentation](L12/07_interactive-and-video-segmentation.md) — map-based interactive segmentation, and tracking objects across video frames.
8. [Project transfer](L12/08_project-transfer.md) — deciding whether Segment Anything fits your project, and planning your prompts and quality checks.

Pages 1–2 build the conceptual foundation, page 3 bridges to geospatial data, page 4 covers prompting, page 5 is the main hands-on workflow, and pages 6–7 extend it to scale and video. Page 8 transfers everything to your project.

---

## 5. Project framing

While reading this lesson, keep your own project in mind and consider:

- Does your project have a feature type visually distinctive enough for a general-purpose model to find, or does it need domain-specific training?
- Which prompt type, text, point, or box, would most naturally describe your target feature?
- How would you check whether a zero-shot result is trustworthy enough to use, beyond a visual glance?
- Would your imagery and target scale require batch processing, tiling, or neither?

---

## 6. Before class

- Review the L07 and L08 lessons on segmentation, since SAM's output (masks, confidence scores) parallels what you already produced there.
- Make sure `geoai` and `segment-geospatial` are installed, and request Hugging Face access approval for SAM 3 ahead of time, since approval is not instant.
- Think of one visually distinctive feature in your own project area that a general-purpose model might plausibly recognize without training.

---

## 7. After this lesson

After working through this lesson, you should have:

- A clear decision on whether Segment Anything fits your project, or whether a task-specific trained model from earlier lessons is the better investment
- A working understanding of the full `samgeo` workflow: prompting, confidence filtering, vectorization, and scaling to batches or large rasters
- A first sketch of your own prompt strategy and quality-control plan, to be developed further on the final page
