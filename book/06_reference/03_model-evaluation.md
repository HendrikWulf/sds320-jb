---
site:
  outline_maxdepth: 2
---
# Model evaluation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Choosing useful ways to assess spatial analytics results
</div>
<!-- markdownlint-enable MD033 -->

---

## Why evaluation matters

A result is not finished just because a model produced output. You still need to check whether that output actually supports your research question, communicate how good it is, and be honest about where it falls short. Evaluation is what turns a prediction into evidence you can use in a report.

---

## Evaluation is project-specific

There is no single metric that fits every project. The right evaluation approach depends on your task type, your data, your method, and what validation information you actually have available. A metric that is standard for one task can be meaningless, or actively misleading, for another. Use the sections below to identify a starting point for your specific task, not a fixed requirement.

---

## Evaluation by task type

### Classification

Image or tabular classification assigns observations to discrete classes.

Useful evaluation tools include:

| Measure | Main question |
| --- | --- |
| {term}`Accuracy` | What proportion of all predictions is correct? |
| {term}`Precision` | When the model predicts a class, how often is it correct? |
| {term}`Recall` | How much of the true class does the model find? |
| {term}`F1-score` | How well are precision and recall balanced? |
| {term}`Confusion Matrix` | Which classes are being confused? |

Accuracy can be misleading when classes are strongly imbalanced.

For example, if 95% of samples belong to one class, a model that nearly always predicts that class can have high accuracy while performing poorly on the minority class.

A confusion matrix helps reveal this problem because it shows the pattern of class-to-class errors.

```{tip}
When one class is particularly important to your research question, inspect its precision and recall rather than relying only on overall accuracy.
```

---

### Object detection

{term}`Object Detection` evaluates both:

1. **what** was detected,
2. **where** it was detected.

A prediction is usually considered a {term}`True Positive` when:

- the predicted class is correct,
- the predicted bounding box overlaps sufficiently with a reference object,
- that reference object has not already been matched to another prediction.

A {term}`False Positive` is an unmatched or incorrect detection. A {term}`False Negative` is a reference object that the model missed.

#### Bounding-box overlap

{term}`Intersection over Union` is often abbreviated as {abbr}`IoU (Intersection over Union)`.

IoU compares the overlap between predicted and reference boxes:

$$\text{IoU} = \frac{\text{predicted positive pixels} \cap \text{reference positive pixels}}{\text{predicted positive pixels} \cup \text{reference positive pixels}}$$

Higher IoU means stronger spatial agreement.

#### Precision and recall

Detection precision asks:

> Of the objects predicted by the model, how many are correct?

Recall asks:

> Of the real objects in the reference data, how many did the model find?

The confidence threshold creates a trade-off:

```text
lower threshold
→ more predictions
→ often higher recall
→ potentially more false positives

higher threshold
→ fewer predictions
→ potentially higher precision
→ potentially more false negatives
```

The appropriate threshold depends on the application.

For example, a screening workflow followed by manual inspection may tolerate more false positives than a workflow that automatically counts objects.

#### Average precision

{term}`Average Precision` summarizes the precision-recall relationship for one class across confidence thresholds.

{term}`Mean Average Precision` averages performance across classes and, depending on the convention, may also summarize performance across several IoU thresholds.

Do not report mAP without explaining which definition or threshold convention you used.

---

### Semantic segmentation

{term}`Semantic Segmentation` assigns a class label to every pixel.

Evaluation therefore compares predicted and reference masks at pixel level.

Common tools include:

- IoU,
- precision,
- recall,
- F1-score,
- confusion matrices,
- class-specific performance.

For a binary mask:

$$\text{IoU} = \frac{\text{predicted positive pixels} \cap \text{reference positive pixels}}{\text{predicted positive pixels} \cup \text{reference positive pixels}}$$

IoU is particularly informative because it measures spatial overlap directly.

#### Multi-class segmentation

For several classes, overall performance may hide a weak minority class.

Check, where possible:

- per-class IoU,
- per-class precision and recall,
- the confusion matrix,
- visual examples of difficult classes.

This matters when the project contains {term}`Class Imbalance`.

A land-cover model that performs very well on common tree-canopy pixels but poorly on a rare wetland class may still have a high aggregate score.

---

### Instance segmentation

{term}`Instance Segmentation` combines object detection with pixel-level masks.

Evaluation can therefore consider two related questions:

1. Was the individual object detected?
2. How accurately does its predicted mask represent the object?

Useful checks can include:

- object-level true positives, false positives and false negatives,
- bounding-box IoU,
- mask IoU,
- precision-recall behaviour,
- AP or mAP where appropriate,
- visual inspection of adjacent or overlapping instances.

For spatial projects, pay particular attention to:

- merged neighboring objects,
- one object split into several instances,
- missed small objects,
- inaccurate boundaries.

The metric you emphasize should reflect whether your research question depends mainly on **counts**, **locations**, **boundaries** or some combination of them.

---

### Change detection

{term}`Change Detection` usually compares observations from different times.

For binary change/no-change output, familiar classification measures can be used:

- precision,
- recall,
- F1-score,
- confusion matrix.

But change detection has additional spatial and temporal challenges.

#### False change

A predicted change can result from:

- misregistration,
- clouds or shadows,
- seasonal differences,
- illumination differences,
- sensor differences,
- actual land-surface change.

#### Missed change

A true change may be missed because it is:

- small relative to the pixel size,
- spectrally subtle,
- partly obscured,
- poorly represented in the training data.

Visual comparison of the input dates remains useful because an aggregate metric cannot explain *why* a particular area was labelled as changed.

Also consider whether the two dates are genuinely comparable. Good model evaluation cannot repair inappropriate temporal input data.

---

### Regression

{term}`Regression` predicts continuous values.

Examples might include:

- canopy height,
- temperature,
- biomass,
- environmental indicators.

#### Mean absolute error

{term}`Mean Absolute Error` is often abbreviated as {abbr}`MAE (Mean Absolute Error)`.

MAE measures the average absolute prediction error:

$$\text{MAE} = \frac{1}{n} \sum_{i=1}^{n} \vert{}y_i - \hat{y}_i\vert{}$$

Because it uses the same unit as the target, it is usually easy to interpret.

#### Root mean squared error

{term}`Root Mean Squared Error` is often abbreviated as {abbr}`RMSE (Root Mean Squared Error)`.

RMSE gives relatively more weight to larger errors.

$$\text{RMSE} = \sqrt{\frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2}$$

Comparing MAE and RMSE can help reveal whether a relatively small number of large errors are important.

#### R-squared

{term}`R-squared` is commonly written as \(R^2\).

It describes how much of the target variance is explained by the model.

Do not interpret a high \(R^2\) as proof that predictions are unbiased or spatially well distributed.

#### Residuals

A {term}`Residual` is:

```text
reference value - predicted value
```

Residual plots and maps can reveal patterns hidden by one summary number.

Look for:

- systematic overprediction,
- systematic underprediction,
- changing error magnitude across the target range,
- spatial clusters of large errors.

```{important}
For a spatial regression project, mapping the residuals can be as informative as reporting the overall error metric.
```

---

### Foundation models and embeddings

{term}`Foundation Model` workflows do not always produce a direct prediction that can be evaluated with one standard metric.

Evaluation should match the downstream use.

#### Similarity search

Ask whether nearby items in {term}`Embedding Space` are actually similar in ways relevant to your question.

Useful checks include:

- inspecting nearest-neighbour examples,
- testing several reference locations,
- comparing similarity patterns across different landscapes.

#### Clustering

Clustering is unsupervised, so a cluster is not automatically a meaningful land-cover or environmental class.

Evaluate whether:

- clusters are stable enough to interpret,
- they correspond to meaningful spatial patterns,
- different clusters represent actual differences rather than artifacts,
- the interpretation is supported by imagery or independent information.

#### Lightweight classification

If embeddings are used as features for a classifier, evaluate the classifier using appropriate classification metrics and a defensible train/test strategy.

#### Zero-shot or prompted outputs

For a zero-shot model, qualitative inspection may be an important first step.

If the output becomes central to a quantitative conclusion, strengthen the evaluation with representative reference samples where feasible.

```{note}
A visually plausible foundation-model output is evidence for further investigation, not automatically evidence of accuracy.
```

---

## Spatial evaluation concerns

Spatial data raises evaluation issues that do not show up in the same way with non-spatial data:

- **{term}`Spatial autocorrelation <Spatial Autocorrelation>`** — nearby locations tend to be similar, which can make a random train/validation split overly optimistic if training and validation points end up close together.
- **{term}`Data leakage <Data Leakage>`** — information from your validation set influencing training, for example through overlapping tiles or shared source imagery.
- **Spatially separated validation** — holding out an entire region, rather than randomly selected points, gives a more honest estimate of how a model performs somewhere it has never seen.
- **{term}`Class imbalance <Class Imbalance>`** — rare but important classes can look fine in an aggregate metric while performing poorly individually.
- **Scale and resolution** — a metric computed at one resolution is not automatically comparable to the same metric at a different resolution or study area size.
- **Uncertainty** — a single point estimate (an accuracy, an RMSE) rarely captures how confident a model actually was; use the uncertainty concepts from image translation and diffusion-based workflows where relevant.
- **Visual plausibility versus quantitative accuracy** — a result can look convincing and still be wrong, and a result can look rough and still be numerically accurate. Do not let either one substitute for the other.

---

## Choosing an evaluation strategy

Work through this checklist for your own project:

- [ ] I can name the specific metric(s) that match my task type from the sections above.
- [ ] I know whether my validation data is spatially separated from my training data, or just randomly split.
- [ ] I have checked for class imbalance in my labels or targets.
- [ ] I am reporting more than one metric, or a metric plus a visual check.
- [ ] I have looked at individual results (a few predictions, a residual map, a confusion matrix), not only an aggregate summary.
- [ ] I can state, in one sentence, what my evaluation does and does not prove about my project's question.

---

## Common pitfalls

- **Reporting only one metric.** A single number is rarely enough to judge whether a result is trustworthy for your specific question.
- **Using random splits when spatial separation matters.** This can make performance look better than it will be on genuinely new locations.
- **Ignoring class imbalance.** An aggregate metric can look strong while a class you actually care about performs poorly.
- **Evaluating on visually similar nearby data.** Validation data that is spatially or temporally very close to training data tends to overestimate real-world performance.
- **Overstating model performance.** Report metrics as they are, with their limitations, rather than rounding up to the most favorable interpretation.
- **Confusing visual quality with accuracy.** A polished-looking map or a sharp super-resolved image is not the same as a validated one.

---

## Mini task

Choose one evaluation strategy for your own project, and write two or three sentences explaining why it fits your specific question, data, and validation situation. If you are unsure between two approaches, state what would help you decide.

---

## Key takeaways

- Evaluation should match your task type and your project's actual question, not a generic default.
- Spatial data introduces specific concerns, autocorrelation, leakage, spatial separation, that non-spatial evaluation advice often misses.
- A metric and a visual or spatial check together are more trustworthy than either alone.
- Being explicit about what your evaluation does not prove is as important as reporting what it does.
