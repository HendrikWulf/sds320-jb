# L11 - Regression

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Predicting a continuous value per pixel instead of a category
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

L07 through L10 all produced categorical or discrete output: a class per pixel, a mask per object, a change or no-change flag. This lesson introduces a different question. {term}`Pixel-level regression <Pixel-Level Regression>` predicts a single continuous value for every pixel, canopy height in meters, an NDVI value between −1 and 1, a biomass density in Mg/ha, rather than choosing among a fixed set of labels.

The architectures you already know from segmentation carry over almost unchanged. What changes is the output layer, the loss function, and how you evaluate the result, all of which this lesson covers using a Landsat-to-NDVI case study.

---

## 2. Motivation

Many of the quantities that matter most in Earth science are not categorical at all. "Dense vegetation" is a far cruder answer than "NDVI of 0.72." Canopy height, biomass, soil moisture, and population density all vary smoothly across a landscape, and forcing them into discrete bins throws away exactly the information a scientific or policy application usually needs.

{term}`Regression` closes this gap. Where classification tells you *what* is present and segmentation tells you *where*, regression tells you *how much* of a measurable quantity exists at each location, output that plugs directly into further calculation, without the information loss of discretizing a continuous variable into categories.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- Decide when a project question calls for regression rather than classification or segmentation
- Explain what changes, and what stays the same, when adapting a segmentation architecture for regression
- Prepare paired image and target rasters for a regression task, and choose an appropriate loss function
- Train a pixel-level regression model with `geoai` and monitor its training history
- Run wall-to-wall inference on new imagery, including on a different year than the model was trained on
- Evaluate a regression model with RMSE, MAE, R-squared, and residual analysis, not aggregate metrics alone

---

## 4. Lesson roadmap

1. [When regression fits](L11/01_when-to-use-pixel-regression.md) — typical continuous targets, and when classification or segmentation is the better choice instead.
2. [Classification vs. regression](L11/02_classification-vs-regression.md) — the conceptual shift from categorical masks to floating-point target rasters.
3. [Architectures & losses](L11/03_regression-architectures-and-losses.md) — adapting segmentation architectures for regression, and choosing MSE, MAE, or Huber loss.
4. [Data & tiles](L11/04_regression-data-and-tiles.md) — preparing paired image and target rasters, using Landsat and NDVI as the running example.
5. [Training a regressor](L11/05_training-a-pixel-regressor.md) — the main hands-on workflow: training a U-Net regression model with `geoai`.
6. [Inference & temporal prediction](L11/06_inference-and-temporal-prediction.md) — wall-to-wall prediction, and applying a trained model to a new year's imagery.
7. [Evaluation & residuals](L11/07_evaluation-and-residual-analysis.md) — RMSE, MAE, R-squared, correlation, and reading residual maps.
8. [Project transfer](L11/08_project-transfer.md) — deciding whether regression fits your project, and sketching your target, data, and evaluation plan.

Pages 1–2 build the conceptual foundation, pages 3–4 cover architecture and data preparation, page 5 is the main training workflow, and pages 6–7 extend it with inference and evaluation. Page 8 transfers everything to your project.

---

## 5. Project framing

While reading this lesson, keep your own project in mind and consider:

- Is your project's target genuinely continuous, or would a smaller number of meaningful categories actually serve your question just as well?
- What reference data (field measurements, LiDAR, a derived index) could you pair with imagery to train a regression model?
- What is the known valid range for your target variable, and how would you enforce it during training and inference?
- Would a single aggregate metric (like RMSE) be enough to trust a regression result, or would you need to look at spatial error patterns too?

---

## 6. Before class

- Review the L07 semantic segmentation lesson, since regression architectures build directly on the encoder-decoder pattern from that lesson.
- Make sure `geoai` is installed and importable in your environment.
- Think of one continuous variable relevant to your own project (a physical quantity, an index, or a measurable rate).

---

## 7. After this lesson

After working through this lesson, you should have:

- A clear decision on whether pixel-level regression fits your project, or whether classification or segmentation better matches your question
- A working understanding of the full regression workflow, from paired tiles through training, inference, and residual-aware evaluation
- A first sketch of your own regression target, reference data source, and evaluation plan, to be developed further on the final page
