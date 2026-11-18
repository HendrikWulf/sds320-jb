---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Deciding whether pixel regression fits your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

This lesson covered a complete regression workflow twice: on Sentinel-2 {term}`NDVI <Normalized Difference Vegetation Index>` along the Beni river, where the correct answer was known exactly, and on vegetation height at two Swiss forest sites, a real measurement that no formula delivers. This page is not about learning anything new. It is about deciding, concretely, whether {term}`pixel-level regression <Pixel-Level Regression>` fits your SDS320 project, and sketching the target, reference data, validation design and evaluation your project would need.

---

## 2. What the two case studies showed

| Lesson from the case studies | Measured where | What it means for your project |
| --- | --- | --- |
| A random split of overlapping tiles puts most validation pixels into training | Page 4: more than 90% | Split by location (or date), with a gap between the areas |
| Without the decisive band ({term}`NIR <Near-Infrared>`), the model still finds the broad pattern, with clearly larger errors | Page 5 | The input bands matter more than the architecture |
| Input scaling errors fail silently | Pages 4 and 8 | Check value ranges and data types of what enters the model |
| Wall-to-wall prediction needs overlap, clipping and {term}`nodata <NoData Value>` handling | Page 6 | Plan these settings; mask nodata in your own statistics |
| Errors concentrate in specific places and value ranges | Pages 7 and 8: land–water boundaries, tall trees | Report errors for the groups your question depends on |
| Simple baselines put a model's scores into perspective | Page 8 | Always compare with the mean and a simple model |
| Transfer to a new date or site costs accuracy | Pages 7 and 8 | Test on a held-out date or site before relying on predictions there |

---

## 3. Project checklist

Work through this list for your own project:

- [ ] I can name the continuous target my project needs, its unit, and its valid range, and I have confirmed that it is genuinely continuous rather than better served by categories.
- [ ] The target cannot simply be computed from my input data (otherwise I compute it instead).
- [ ] I have a source of reference data (field measurements, {term}`LiDAR`, a national product, a derived index), and I know how it was produced, when, and with what accuracy.
- [ ] My imagery and reference data are aligned on the same grid, from comparable dates, and I have checked the data types and value ranges of both.
- [ ] I know how my input is scaled and whether that matches what the model expects.
- [ ] I have a validation design that holds out an area, a site or a date, not random tiles.
- [ ] I have a starting configuration (bands, architecture, loss), with a reason for each choice, and a results table to record experiments.
- [ ] I will compare my model with at least one simple {term}`baseline <Baseline Model>`.
- [ ] I will report errors for the value ranges and areas that matter for my question, plus a residual map.

---

## 4. Decision points

**Regression, classification, or no model?** Revisit page 1. If a handful of categories answers your question, segmentation is simpler. If the target is a formula of your bands, compute it. Regression fits when the value matters and must be estimated.

**Which reference data, and how good is it?** Your map cannot be better than the reference data it learns from. Find out how it was produced (the Swiss {term}`vegetation height model <Vegetation Height Model>`, for example, comes from stereo matching and is least reliable in deep shadows) and whether its date matches your imagery.

**Which {term}`loss function <Loss Function>`?** {term}`Mean squared error <Mean Squared Error>` (MSE) as the default, since it optimises the squared error that {term}`RMSE <Root Mean Squared Error>` reports; {term}`Huber loss <Huber Loss>` or {term}`mean absolute error <Mean Absolute Error>` (MAE) if your reference data contain gross errors. Remember that `geoai`'s Huber threshold is 1 in target units (page 3), and compare at least two options on your held-out data.

**How will you validate?** Decide on the held-out area, site or date before training, and keep it untouched until the end. If you need predictions for other years or places, make sure your validation includes that kind of transfer.

**What will you report?** At least RMSE or MAE (in target units), the {term}`bias <Bias>`, {term}`R² <R-squared>`, a baseline comparison, a scatter plot and a residual map, plus errors for the value range your question depends on.

---

## 5. Common pitfalls

- **Choosing regression without a realistic reference data source.** Regression cannot proceed without paired continuous reference measurements; confirm they exist before committing.
- **Validating on random tiles.** Overlap and {term}`spatial autocorrelation <Spatial Autocorrelation>` make such scores far too optimistic.
- **Reporting only one metric for the whole map.** A good overall RMSE can hide large errors exactly where your project needs accuracy.
- **Using the extremes of a regression map at face value.** Models compress the range; the highest and lowest predicted values are the least reliable.
- **Assuming a model will generalize across time or space without checking.** Test it on a held-out date or site, even a small one, before relying on it for a project conclusion.

---

## 6. Mini deliverable

Produce a short regression project plan (half a page is enough) that states:

1. Your continuous target variable, its unit and valid range, and why it needs regression rather than classification, segmentation or a formula.
2. Your reference data source, how it was produced, and how you will pair it with imagery (grid, dates, scaling).
3. Your validation design: what is held out (area, site or date) and why that matches how you will use the model.
4. Your starting configuration (bands, architecture, loss function), with a one-sentence justification for each.
5. Your evaluation plan: metrics, at least one baseline, and the groups or areas for which you will report errors separately.

---

## 7. Reflection questions

1. Does your project's target genuinely need a continuous value, or would a well-chosen set of categories answer your question with less data and complexity?
2. What is your realistic source of reference data, and how confident are you in its quality, its date and its coverage?
3. If your project needs predictions beyond the area or period of your reference data, how will you test whether that transfer actually works?
4. Which value range or area matters most for your question, and how large would the error there have to be before your conclusion changed?
5. How would you explain the difference between your model's overall accuracy and its accuracy where your project cares most, to someone who only reads the summary?
