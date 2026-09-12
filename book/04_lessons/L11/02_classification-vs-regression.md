---
site:
  outline_maxdepth: 2
---

# Classific. vs. regression

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Telling apart discrete-class problems from continuous-value problems
</div>
<!-- markdownlint-enable MD033 -->

---

You now know where regression applies. This page makes the conceptual shift explicit: what actually changes, mechanically, when a task moves from categorical prediction to continuous prediction, before the next page covers architecture and loss function choices in more depth.

---

## 1. Motivation

`geoai`'s regression functions look deceptively similar to the segmentation functions from L07, same general shape of call, same kind of paired data. The differences are precise and consistent, and knowing exactly what they are will keep you from applying a segmentation habit (like checking IoU) where it no longer makes sense.

---

## 2. Core idea

{term}`Classification` (whether scene-level or pixel-level) selects among a fixed, predefined set of discrete labels. {term}`Regression` predicts a continuous numeric value with no fixed set of possible answers. This single distinction cascades through four separate parts of the pipeline: the output layer, the loss function, the evaluation metrics, and the label format itself.

---

## 3. What changes

### A. Output layer

A classification model outputs N channels, one score per class, followed by a softmax activation that turns those scores into probabilities summing to one. A regression model outputs a single channel with no activation at all, or a ReLU activation when the target is known to be non-negative (height, biomass), since ReLU simply clips negative outputs to zero rather than forcing values into a probability-like range.

### B. Loss function

Classification typically uses cross-entropy loss, which penalizes assigning low probability to the correct class. Regression uses a {term}`loss function <Loss Function>` built around the *magnitude* of numeric error, mean squared error being the most common starting point, covered in detail on the next page. The underlying philosophy differs too: in classification, a prediction is either right or wrong; in regression, closer predictions are always better, even when not exact.

### C. Evaluation metrics

Classification evaluation (accuracy, {term}`IoU <Intersection over Union>`, F1) asks "how often was the prediction the correct category?" Regression evaluation asks a different question entirely: "how close, numerically, were the predictions to the actual values?" This calls for a different metric family (RMSE, MAE, R-squared), covered fully on the evaluation page later in this lesson.

### D. Label format

A classification or segmentation label is an integer {term}`mask <Mask>`: each pixel stores a class index. A regression label is a floating-point raster: each pixel stores an actual continuous value, an NDVI reading, a height in meters, whatever the target variable is. This is not a minor formatting detail; storing a continuous target as an integer type silently destroys precision, a pitfall worth remembering once you start preparing your own regression data on the next page.

```{admonition} One mental model for all four changes
:class: note
Every change on this page follows from the same root distinction: classification chooses among options, regression estimates a number. Once that is clear, the output layer, loss function, evaluation metrics, and label format changes are just the natural consequences of that single shift, not four separate things to memorize independently.
```

---

## 4. Python reactivation

Data type matters more here than in earlier lessons. A classification mask is typically stored as an integer array (`uint8` or similar), fine since class indices are small whole numbers. A regression target needs a floating-point array (`float32` is standard), since it must represent fractional values precisely; if you ever load or save a regression target and see it silently rounded to whole numbers, check whether it was cast to an integer type somewhere in your pipeline.

---

## 5. Common pitfalls

- **Storing a continuous target as an integer array.** This silently destroys the fractional precision regression depends on; always confirm your target raster's dtype is a floating-point type.
- **Applying classification evaluation habits to a regression result.** Checking "accuracy" or IoU on a regression output does not make sense; the next page's metrics answer a genuinely different question.
- **Assuming the architecture itself needs to change substantially.** As the next page shows, the encoder-decoder backbone barely changes; the differences concentrate in the output layer, loss, and evaluation.
- **Forgetting that "close" is meaningfully better in regression, unlike classification.** A prediction of 0.68 when the true NDVI is 0.70 is a small, acceptable error; there is no equivalent partial credit in a classification setting.

---

## 6. Mini task

A colleague trained what they believe is a regression model to predict building height, but all their outputs come out as whole numbers (3, 7, 12, never 7.4 or 12.1). Using the ideas from this page, suggest the most likely cause.

:::{dropdown} Sample solution
:class: note

The most likely cause is that the target raster (or the output array) was stored or cast as an integer type at some point in the pipeline, silently rounding away the fractional precision a continuous height value should have. The fix is to confirm the target raster is loaded and saved as a floating-point type (`float32`) throughout data preparation, training, and inference, exactly the dtype pitfall highlighted on this page.
:::

---

## 7. Key takeaways

- The classification-to-regression shift cascades through four pipeline stages: output layer, loss function, evaluation metrics, and label format.
- Regression uses a single output channel (no activation, or ReLU for non-negative targets), instead of N channels with softmax.
- Regression loss functions penalize the magnitude of numeric error; regression evaluation metrics measure numeric closeness, not category correctness.
- Regression labels must be stored as floating-point rasters; storing them as integers silently destroys the precision the task depends on.
