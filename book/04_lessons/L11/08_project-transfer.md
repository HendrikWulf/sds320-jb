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

This lesson covered a full regression workflow: when it fits, how it differs from classification, architecture and loss choices, data preparation, training, temporal inference, and residual-aware evaluation. This page is not about learning anything new. It is about deciding, concretely, whether pixel-level regression fits your SDS320 project, and sketching the specific choices, target, data, loss, and evaluation plan, your project would need.

---

## 2. Project checklist

Work through this list for your own project:

- [ ] I can name the specific continuous target my project needs to predict, and confirm it is genuinely continuous rather than better served by categories.
- [ ] I have identified a source of paired reference data (field measurements, LiDAR, a derived index) to train on.
- [ ] I know my target's valid physical range, and have a plan to enforce it during both tile creation and inference.
- [ ] I have a starting architecture and loss function choice, with a reason for each.
- [ ] I have a plan to evaluate results with both aggregate metrics and spatial residual analysis, not metrics alone.
- [ ] If my project needs predictions for a different time period, I have a plan to test temporal generalization explicitly.

---

## 3. Decision points

**Regression, or classification/segmentation?** Revisit the framework from the first page of this lesson. If your target genuinely needs a specific continuous value, and you have or can obtain reference data, regression fits. If a handful of categories would serve your question just as well, the earlier lessons' methods are usually simpler.

**Which loss function?** Match your choice to your reference data's likely quality. MSE if worst-case errors matter most and your data is clean; MAE if you want proportional treatment of all errors; Huber as a balanced default for noisier, real-world reference data.

**How will you enforce your target's valid range?** Decide on both a tile-creation filter and an inference-time clip, rather than relying on either alone, matching the two-part approach from the architecture and losses page.

**What evaluation will you actually report?** Plan to report at least one aggregate metric (RMSE or R-squared) alongside a residual map or scatter plot, so your project's evaluation reveals spatial patterns, not just a single number.

**Does your project need temporal generalization?** If you need predictions for a time period without direct reference data, plan to test the model on a held-out time period explicitly, the same check performed two pages ago, rather than assuming it will transfer.

---

## 4. Common pitfalls

- **Choosing regression without a realistic reference data source.** Unlike classification, regression cannot proceed without paired continuous reference measurements to train on; confirm this exists before committing.
- **Reporting only one evaluation metric in a project write-up.** As this lesson emphasized, RMSE, MAE, R-squared, and a residual map each reveal something the others do not; a single number is an incomplete picture.
- **Forgetting to enforce a target's valid range.** This shows up as implausible predictions (negative heights, out-of-range indices) that undermine confidence in an otherwise reasonable model.
- **Assuming a model will generalize across time or space without checking.** Test this explicitly, even informally, before relying on it for a project conclusion.

---

## 5. Mini deliverable

Produce a short regression project plan (half a page is enough) that states:

1. Your continuous target variable and why it needs regression rather than classification or segmentation.
2. Your reference data source and how you will pair it with imagery.
3. Your target's valid range and your plan to enforce it during tiling and inference.
4. Your planned architecture and loss function, with a one-sentence justification for each.
5. Your evaluation plan, including at least one metric and one spatial check (residual map or scatter plot).

---

## 6. Reflection questions

1. Does your project's target genuinely need a continuous value, or would a well-chosen set of categories answer your question with less data and complexity?
2. What is your realistic source of reference data, and how confident are you in its quality and coverage?
3. If your project needs temporal or spatial generalization beyond your reference data's coverage, how will you test whether that generalization actually holds?
4. If your evaluation revealed a spatial pattern in residuals, similar to the example on the evaluation page, how would you decide whether it matters for your specific project question?
5. How would you communicate the difference between your model's aggregate accuracy and its performance in the specific areas your project cares about most?
