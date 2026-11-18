# L10 - Change detection

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Comparing imagery from two points in time to find what changed
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

L09 introduced {term}`image translation <Image Translation>`: transforming one image into another, generated, image. This lesson compares two *real* observations of the same place instead. {term}`Change detection <Change Detection>` takes imagery from two or more dates and identifies where the surface has genuinely changed, producing a binary mask, a categorical map, or a continuous measure of change intensity.

This is also the point in the course where you start comparing images to each other rather than analyzing a single scene, a shift that carries forward into later, more advanced project workflows. The interpretation habits from L09, checking whether a difference reflects a real change versus something else entirely, apply directly here too.

---

## 2. Motivation

Many of the most consequential geospatial questions are fundamentally about change: how fast is a city expanding, how much forest was cleared this year, how far did a flood reach, how much of a coastline eroded. Answering any of these requires comparing imagery across time, not just describing a single snapshot.

Change detection is also deceptively easy to get wrong. Two images of the same location taken months apart differ not only because the surface changed, but because of sun angle, atmospheric conditions, sensor calibration, and seasonal vegetation cycles. A substantial part of this lesson is about learning to tell these apart, since a change detection workflow that cannot distinguish real change from irrelevant variation produces results no one should trust.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- Decide when change detection is the right tool, and what form of output (binary, categorical, or continuous) your project actually needs
- Explain what makes two images genuinely comparable, and identify the main obstacles when they are not
- Apply traditional, interpretable baseline methods: {term}`image differencing <Image Differencing>` and {term}`change vector analysis <Change Vector Analysis>`
- Explain why deep learning models, particularly {term}`siamese networks <Siamese Network>`, can outperform pixel-wise comparison
- Run a deep learning change detection workflow with `geoai`, and interpret its full set of outputs
- Adjust detection thresholds deliberately and export results as GIS-ready files
- Train and evaluate your own siamese change detection model on labeled Sentinel-2 image pairs, and judge which training choices actually matter

---

## 4. Lesson roadmap

1. [When change detection fits](L10/01_when-to-use-change-detection.md) — typical use cases, the three output types, and the three families of methods.
2. [Change types and challenges](L10/02_change-types-and-challenges.md) — what kinds of change exist, and what makes two images actually comparable.
3. [Traditional methods](L10/03_traditional-change-detection.ipynb) — image differencing, index differencing and change vector analysis on Sentinel-2 imagery of Egypt's New Administrative Capital near Cairo, as interpretable baselines.
4. [Deep learning approaches](L10/04_deep-learning-for-change-detection.md) — why spatial context helps, how siamese networks compare image pairs, which data they learn from, and how change maps are evaluated.
5. [The ChangeStar workflow](L10/05_changestar-workflow.ipynb) — the main hands-on workflow: detecting new buildings in {term}`VEXCEL` aerial imagery of a housing estate in northern Osaka, Japan.
6. [Reading the results](L10/06_visualising-and-interpreting-results.ipynb) — interpreting {term}`ChangeStar`'s full output: change maps, probabilities, per-date building masks, and a from–to map of new and removed buildings.
7. [Thresholds and exports](L10/07_thresholds-model-variants-and-exports.ipynb) — tuning sensitivity, comparing model variants and a pixel-wise baseline, and exporting attributed GIS-ready polygons.
8. [Training your own model](L10/08_training-a-change-detector.ipynb) — training and evaluating an {abbr}`FC-Siam-diff (Fully Convolutional Siamese network, difference variant)` network on labeled Sentinel-2 pairs, with settings you can change and compare.
9. [Project transfer](L10/09_project-transfer.md) — designing a change-detection workflow for your own project.

Pages 1–2 build the conceptual foundation, pages 3–4 cover two different approaches (traditional and deep learning), page 5 is the main hands-on workflow, and pages 6–7 extend it with interpretation, tuning, and export. Page 8 shows what to do when no pretrained model fits your change type: training your own. Page 9 transfers everything to your project.

---

## 5. Project framing

While reading this lesson, keep your own project in mind and consider:

- Does your project need to know *that* something changed, *what kind* of change occurred, or *how much* change happened?
- What two (or more) dates would you realistically compare, and are they close enough in season to be genuinely comparable?
- Would a simple, interpretable baseline (image differencing or {term}`CVA <Change Vector Analysis>`) already answer your question, or does your project need the spatial context a deep learning model provides?
- If you need deep learning, does a pretrained model exist for your type of change, or would you need labeled image pairs to train your own?
- How would you validate a change detection result without exhaustive ground-truth data?

---

## 6. Before class

- Review the L07 semantic segmentation lesson, since ChangeStar's per-date outputs are segmentation masks.
- Make sure `geoai` and `torchgeo` are installed and importable in your environment.
- Review the L07 binary segmentation workflow, especially reading learning curves and the training, validation and test split, since page 8 trains a model in the same way.
- Think of one change process relevant to your own project area (growth, loss, damage, or a seasonal or disaster-related change).

---

## 7. After this lesson

After working through this lesson, you should have:

- A clear decision on whether change detection fits your project, and which output type it should produce
- A working understanding of both traditional and deep-learning change detection, and when each is the more appropriate choice
- A first sketch of your own change-detection plan: dates, comparability checks, method, threshold, and validation approach
