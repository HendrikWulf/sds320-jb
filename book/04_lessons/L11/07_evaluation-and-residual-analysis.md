---
site:
  outline_maxdepth: 2
---

# Residual analysis

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Evaluating regression models with residuals, MAE and MSE
</div>
<!-- markdownlint-enable MD033 -->

---

You have a trained model and predictions for two years. This page evaluates how good those predictions actually are, using both aggregate metrics and spatial residual analysis, since either one alone can hide a genuine problem the other would catch.

---

## 1. Motivation

A single RMSE value can look perfectly reasonable while masking a specific region where the model performs poorly, exactly the kind of blind spot that matters if your project depends on accuracy in a particular area. Reading metrics and maps together is what catches this.

---

## 2. Core idea

Regression evaluation combines four numeric metrics, each answering a slightly different question about prediction quality, with visual tools, {term}`residual <Residual>` maps and scatter plots, that reveal *where* and *how* errors are structured, information no single number can convey on its own.

---

## 3. Workflow

### A. Three-panel comparison

```{code-cell} python
fig, metrics = geoai.plot_regression_comparison(
    true_raster=train_target,
    pred_raster="ndvi_model/predicted_ndvi_2022.tif",
    title="NDVI Prediction Results",
    cmap="RdYlGn",
    vmin=-0.2,
    vmax=0.8,
    valid_range=(-1.0, 1.0),
)
```

This produces ground truth, prediction, and residual (difference) maps side by side. `valid_range` filters out outlier pixels before computing metrics, keeping the evaluation fair and focused on physically plausible values. The residual panel is where spatial patterns in error become visible directly, information the metrics computed alongside it cannot show on their own.

### B. Scatter plot

```{code-cell} python
fig, metrics = geoai.plot_scatter(
    true_raster=train_target,
    pred_raster="ndvi_model/predicted_ndvi_2022.tif",
    sample_size=50000,
    valid_range=(-1.0, 1.0),
    fit_line=True,
)
```

In an ideal model, points cluster tightly along the 1:1 line. Systematic deviation from that line, for example predictions consistently a bit lower than actual values at the high end, reveals a bias that an aggregate metric alone would not make as visually obvious.

### C. The four standard metrics

- **{term}`RMSE (Root Mean Squared Error) <RMSE (Root Mean Squared Error)>`** measures the standard deviation of prediction errors, in the same units as the target. An RMSE of 0.05 for NDVI means predictions are typically off by about 0.05, with larger errors weighted more heavily than smaller ones.
- **{term}`MAE (Mean Absolute Error)`**, covered two pages ago as a loss function, doubles as an evaluation metric here: the plain average absolute error, less sensitive to outliers than RMSE, and often easier to explain as a simple "typical error."
- **{term}`R-squared (R²)`**, introduced during training two pages ago, measures the proportion of variance in the target the model explains; an R-squared of 0.85 means the model captures 85% of the spatial variation in NDVI.
- **{term}`Pearson correlation <Pearson Correlation>`** measures the linear relationship between predictions and actual values, capturing whether the model ranks pixels correctly even in the presence of a systematic bias that RMSE or MAE alone might not clearly reveal.

### D. Reading residual maps

```{admonition} A residual map answers a question metrics cannot
:class: important
"What is my average error?" and "where is my model wrong?" are different questions. A residual map can reveal that errors concentrate near water bodies, in shadowed terrain, or in areas of mixed land cover, exactly the kind of pattern that would be invisible in a single RMSE number but highly relevant for deciding whether your model is trustworthy in the specific area your project cares about.
```

---

## 4. Python reactivation

No new Python syntax here; this page mainly interprets the outputs of functions with the same keyword-argument calling pattern used throughout this lesson.

---

## 5. Common pitfalls

- **Trusting a single aggregate metric without checking the residual map.** As emphasized above, a good RMSE can coexist with a specific, problematic region your project depends on.
- **Confusing R-squared with correlation.** They are related but not identical; R-squared measures explained variance (and can be low even when correlation is reasonably high, if there is a systematic bias), while correlation measures the strength of the linear relationship specifically.
- **Skipping `valid_range` during evaluation.** A handful of outlier pixels can meaningfully distort RMSE and MAE if they are not filtered out before computing metrics.
- **Evaluating only on the training-year raster.** As the previous page emphasized, checking performance on new-year imagery (even informally) is what actually tests generalization, not just fit to familiar data.

---

## 6. Mini task

Your model reports RMSE of 0.06 and R-squared of 0.82 on the 2022 training raster. The residual map shows small, scattered errors almost everywhere, except a spatially coherent patch of consistently high positive residuals along a reservoir shoreline. What would you investigate first, and why?

:::{dropdown} Sample solution
:class: note

The shoreline patch is worth investigating first, since it is spatially coherent (not just noise) and represents a systematic, localized failure mode the aggregate RMSE and R-squared do not reveal on their own. A reasonable first hypothesis is that mixed water-vegetation pixels along the shoreline, or seasonal water-level changes between the imagery and NDVI reference dates, are producing values the model was not well trained to handle; checking a few of these coordinates directly against both dates' imagery would help confirm or rule this out before deciding whether it matters for your project's specific area of interest.
:::

---

## 7. Key takeaways

- RMSE, MAE, R-squared, and Pearson correlation each answer a slightly different question about prediction quality; no single one tells the whole story.
- Residual maps reveal spatial error patterns invisible in aggregate metrics, such as errors concentrated near water, shadows, or mixed land cover.
- Scatter plots of predicted versus actual values reveal systematic bias that a single error metric can obscure.
- Filtering to a valid range before computing metrics keeps evaluation fair and avoids outlier pixels distorting the result.
