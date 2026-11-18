# L11 - Regression

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Predicting a continuous value per pixel instead of a category
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

L07 through L10 all produced categorical or discrete output: a class per pixel, a mask per object, a change or no-change flag. This lesson introduces a different question. {term}`Pixel-level regression <Pixel-Level Regression>` predicts a single continuous value for every pixel, such as {term}`canopy height <Canopy Height>` in metres, an {term}`NDVI <Normalized Difference Vegetation Index>` value between −1 and 1, or a biomass density in Mg/ha, rather than choosing among a fixed set of labels.

The architectures you already know from segmentation carry over almost unchanged. What changes is the output layer, the {term}`loss function <Loss Function>`, and how you evaluate the result. The lesson works through two case studies: {term}`Sentinel-2` imagery of the Beni river in Bolivia, where a model learns NDVI in 2018 and is tested on 2026, and {term}`SWISSIMAGE` aerial photos of two Swiss forest sites, where a model learns vegetation height at one site and is tested on the other.

---

## 2. Motivation

Many of the quantities that matter most in Earth science are not categorical at all. "Dense vegetation" is a far cruder answer than "NDVI of 0.72", and "forest" is less useful than "trees 27 m tall". Canopy height, biomass, soil moisture and population density all vary smoothly across a landscape, and forcing them into discrete bins throws away exactly the information a scientific or policy application usually needs.

{term}`Regression` closes this gap. Where classification tells you *what* is present and segmentation tells you *where*, regression tells you *how much* of a measurable quantity exists at each location. Because these numbers feed directly into further calculations, it also matters more than ever to know how large their errors are, and where.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- Decide when a project question calls for regression rather than classification, segmentation, or a simple calculation
- Explain what changes, and what stays the same, when adapting a segmentation architecture for regression, and choose a suitable loss function
- Prepare paired image and target rasters, check their scaling, and split them into training and validation areas without leakage
- Train a pixel-level regression model with `geoai`, compare settings systematically, and read its training curves
- Run wall-to-wall inference on new imagery, including a different year and a different site, and handle overlap, clipping and nodata
- Evaluate a regression model with error metrics ({term}`RMSE <Root Mean Squared Error>`, {term}`MAE <Mean Absolute Error>`, {term}`bias <Bias>`, {term}`R² <R-squared>`), {term}`baselines <Baseline Model>` and {term}`residual <Residual>` analysis, not aggregate metrics alone

---

## 4. Lesson roadmap

1. [When regression fits](L11/01_when-to-use-pixel-regression.md) — typical continuous targets and their reference data, and when classification, segmentation or a formula is the better choice.
2. [Classification vs. regression](L11/02_classification-vs-regression.md) — the conceptual shift from categorical masks to floating-point target rasters.
3. [Architectures & losses](L11/03_regression-architectures-and-losses.md) — adapting segmentation architectures for regression, choosing {term}`MSE <Mean Squared Error>`, {term}`MAE <Mean Absolute Error>` or {term}`Huber loss <Huber Loss>`, and enforcing valid ranges.
4. [Data & tiles](L11/04_regression-data-and-tiles.ipynb) — Sentinel-2 imagery and NDVI of the Beni river, Bolivia: inspecting, scaling, tiling, and a spatial train/validation split.
5. [Training a regressor](L11/05_training-a-pixel-regressor.ipynb) — the main hands-on workflow: training a {term}`U-Net` with `geoai`, with settings you can change and compare.
6. [Inference & temporal prediction](L11/06_inference-and-temporal-prediction.ipynb) — wall-to-wall prediction for 2018 and 2026, overlap and clipping, and predicted versus real change.
7. [Evaluation & residuals](L11/07_evaluation-and-residual-analysis.ipynb) — RMSE, MAE, bias, R² and correlation by area, year and surface type, and reading residual maps.
8. [Canopy height case study](L11/08_canopy-height-case-study.ipynb) — everything together on a real measurement target: vegetation height from SWISSIMAGE at two forest sites in Graubünden, with baselines.
9. [Project transfer](L11/09_project-transfer.md) — deciding whether regression fits your project, and sketching your target, data, validation and evaluation plan.

Pages 1–3 build the conceptual foundation, pages 4–7 work through one complete workflow step by step, and page 8 applies it to a harder, real-world target. Page 9 transfers everything to your project.

---

## 5. Project framing

While reading this lesson, keep your own project in mind and consider:

- Is your project's target genuinely continuous, or would a smaller number of meaningful categories serve your question just as well?
- What reference data (field measurements, LiDAR, a national product, a derived index) could you pair with imagery to train a regression model, and how accurate is it?
- What is the valid range of your target, and how would you enforce it during training and inference?
- Which area, site or date could you hold out to test whether your model works beyond its training data?
- Would a single aggregate metric (like RMSE) be enough to trust a regression result, or would you need to look at spatial error patterns and specific value ranges too?

---

## 6. Before class

- Review the L07 semantic segmentation lesson, since regression architectures build directly on the encoder-decoder pattern from that lesson.
- Make sure `geoai` (version 0.43 or later) is installed and importable in your environment. Pages 4–8 download about 90 MB of data; training on pages 5 and 8 takes a few minutes with a {term}`GPU` and up to an hour on a {abbr}`CPU (central processing unit)`.
- Think of one continuous variable relevant to your own project (a physical quantity, an index, or a measurable rate).

---

## 7. After this lesson

After working through this lesson, you should have:

- A clear decision on whether pixel-level regression fits your project, or whether classification, segmentation or a direct calculation better matches your question
- A working understanding of the full regression workflow, from paired tiles and a spatial split through training, inference, and residual-aware evaluation with baselines
- A first sketch of your own regression target, reference data source, validation design and evaluation plan, to be developed further on the final page
