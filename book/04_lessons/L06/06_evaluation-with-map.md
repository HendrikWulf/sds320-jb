---
site:
  outline_maxdepth: 2
---

# Evaluation with mAP

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Evaluating a detector with mean Average Precision
</div>
<!-- markdownlint-enable MD033 -->

---

You trained a detector on the previous page and reviewed its training-history plot, which tells you whether training itself went smoothly. This page asks a different question: how good are the actual detections on held-out data? You will use the evaluation split created by `prepare_nwpu_vhr10()` two pages ago, kept separate from training the entire time.

---

## 1. Why evaluation matters

A single headline accuracy number can hide serious problems. A detector might report strong overall performance while completely failing on one class that matters most for your project, for example missing nearly every vehicle while detecting tennis courts perfectly. Reading evaluation results at the right level of detail is what lets you catch this before relying on the model for a project deliverable.

---

## 2. Core idea

Detection evaluation compares predicted boxes against ground-truth boxes using {term}`IoU <Intersection (IoU) Union over>`. Assuming the predicted class is correct, at a chosen IoU threshold:

* A matched prediction is a **true positive** (TP).
* An unmatched prediction is a {term}`False Positive` (FP).
* A missed ground-truth object is a {term}`False Negative` (FN).

From there:

* {term}`Precision` is the fraction of *predicted* detections that are correct.

$$\text{Precision} = \frac{TP}{TP + FP}$$

* {term}`Recall` is the fraction of *ground-truth* objects that were actually detected.

$$\text{Recall} = \frac{TP}{TP + FN}$$

* **{term}`Average Precision (AP)`** summarizes a class's precision-recall trade-off across all confidence thresholds into a single number, by measuring the area under its {term}`precision-recall curve`. Mathematically, it is the integral of precision $P$ as a function of recall $R$:

$$AP = \int_{0}^{1} P(R) dR$$

*(Because real-world predictions are discrete rather than continuous, algorithms calculate this by summing the rectangular areas under the points on the curve: $AP = \sum (R_n - R_{n-1}) P_n$)*

* **{term}`Mean Average Precision (mAP)`** averages AP across all classes. If your dataset has $N$ distinct classes, it is simply the arithmetic mean of all individual class APs:

$$mAP = \frac{1}{N} \sum_{i=1}^{N} AP_i$$

---

## 3. Precision-recall curves

Precision and recall pull in opposite directions as the confidence threshold changes: lowering the threshold catches more real objects (higher recall) but also lets in more wrong detections (lower precision). AP captures this whole trade-off at once, rather than reporting performance at just one arbitrary threshold.

A {term}`Precision-Recall Curve` plots *recall* on the horizontal axis and *precision* on the vertical axis. Each point corresponds to a different confidence threshold. A strong detector can maintain relatively high *precision* while *recall* increases. A weaker detector may gain *recall* only by accepting many incorrect predictions. This curve is especially useful **per class**.

For example, the same model might perform very differently for:

```text
airplane      → large and visually distinctive
vehicle       → smaller and more numerous
ship          → variable background and scale
storage tank  → potentially confused with similar structures
```

Overall performance can hide these differences.

---

## 4. Running and reading an evaluation

### A. Prepare the dataset

The following cells reproduce the setup from the previous page so that the key training variables are explicit.

```{code-cell} python
import os
import geoai
```

```{code-cell} python
url = "https://data.source.coop/opengeos/geoai/NWPU-VHR-10.zip"

data_dir = geoai.download_file(url)

splits = geoai.prepare_nwpu_vhr10(
    data_dir,
    val_split=0.2,
    seed=42,
)
```

```{code-cell} python
output_dir = "nwpu_output"

model_path = geoai.train_multiclass_detector(
    images_dir=splits["images_dir"],
    annotations_path=splits["train_annotations"],
    output_dir=output_dir,
    model_name="fasterrcnn_resnet50_fpn_v2",
    class_names=splits["class_names"],
    num_channels=3,
    batch_size=4,
    num_epochs=10,
    learning_rate=0.005,
    val_split=0.1,
    seed=42,
    pretrained=True,
    verbose=True,
)
```

### B. Run the evaluation

```{code-cell} python
metrics = geoai.evaluate_multiclass_detector(
    model_path=model_path,
    images_dir=splits["images_dir"],
    annotations_path=splits["val_annotations"],
    num_classes=splits["num_classes"],
    class_names=splits["class_names"][1:],  # exclude background
    batch_size=4,
)
```

Note `splits["val_annotations"]` here, the held-out evaluation set from the dataset page, not the internal training-validation split from the previous page. `class_names[1:]` drops `background` from the list, since AP is normally reported only for actual object classes.

### C. Read the overall mAP values

A typical result may look like this:

```text
mAP@0.5: 0.7312
mAP@0.75: 0.4936
mAP@[0.5:0.95]: 0.4428
```

* **mAP@0.5** uses a relaxed IoU threshold: a detection counts as correct if it overlaps the ground truth by at least 50%. This is the traditional threshold, common when approximate location matters more than pixel-perfect boxes.
* **mAP@0.75** requires 75% overlap, rewarding tighter localization, and is noticeably stricter, as the drop from 0.73 to 0.49 in the numbers above shows.
* **mAP@[0.5:0.95]** (the standard COCO metric) averages mAP across IoU thresholds from 0.5 to 0.95 in steps of 0.05. It penalizes imprecise localization more heavily and is the most comprehensive single summary of the three.

Which one matters most depends on your project. If you only need approximate object counts, mAP@0.5 is often sufficient. If your results feed into further GIS work that needs accurate boundaries, favor the stricter thresholds.

### C. Read per-class AP

```text
Per-class AP@0.5:
  AP@0.5/airplane: 0.7106
  AP@0.5/baseball_diamond: 0.7885
  AP@0.5/basketball_court: 0.8957
  AP@0.5/bridge: 0.9052
  AP@0.5/ground_track_field: 0.7081
  AP@0.5/harbor: 0.5322
  AP@0.5/ship: 0.6349
  AP@0.5/storage_tank: 0.5624
  AP@0.5/tennis_court: 0.8967
  AP@0.5/vehicle: 0.6781
```

This is the step that catches problems the overall mAP hides. Large, visually distinctive classes such as tennis courts and bridges score highest here, while smaller or more visually ambiguous classes such as storage tanks and harbors score noticeably lower. If your project depends specifically on one of the weaker classes, that overall mAP@0.5 of 0.73 is not the number to trust.

```{admonition} Always check per-class results
:class: important
An overall mAP can look acceptable while your specific class of interest performs poorly. Before drawing project conclusions from a trained detector, look at the per-class breakdown for exactly the classes your project cares about.
```

---

## 4. Python reactivation

`class_names[1:]` is a Python list slice: it takes every element from index 1 to the end, which drops the first element (`background` at index 0) without modifying the original list. `metrics` is a dictionary; the printed summary above corresponds to keys such as `metrics["mAP_50"]` or similar per-class entries, depending on the exact structure `evaluate_multiclass_detector()` returns.

---

## 5. Common pitfalls

* **Reporting only the headline mAP@[0.5:0.95].** It is the most comprehensive number, but without the per-class breakdown, it can mask exactly the weakness that matters for your project.
* **Treating mAP@0.5 as "50% correct."** The 0.5 refers to the IoU overlap threshold used to judge each detection, not to the fraction of predictions that are right.
* **Evaluating on data the model has already seen.** Always confirm you are pointing `annotations_path` at the held-out evaluation split, not the training or internal-validation data, or your results will be misleadingly optimistic.
* **Ignoring low sample counts per class.** A class with very few examples in the evaluation set can produce an AP score that swings a lot from small changes, and deserves a cautious interpretation.

---

## 6. Mini task

Using the per-class AP@0.5 table above, identify the two weakest-performing classes. Suggest one plausible reason, based on what you know about the NWPU-VHR-10 classes, why these specific classes might be harder to detect than tennis courts or bridges.

:::{dropdown} Sample solution
:class: note

Harbor (0.5322) and storage tank (0.5624) are the weakest classes here. Harbors are visually complex scenes with many small boats and irregular shapes, which likely makes consistent bounding-box localization harder. Storage tanks are small, visually similar to each other, and often appear in dense clusters, all factors that tend to lower detection performance compared to large, geometrically regular, visually distinctive objects like tennis courts or bridges.
:::

---

## 7. Key takeaways

* Precision and recall trade off against each other as the confidence threshold changes; AP summarizes that trade-off for one class across all thresholds, and mAP averages AP across classes.
* mAP@0.5, mAP@0.75, and mAP@[0.5:0.95] apply increasingly strict IoU requirements; choose the one that matches how precise your project's localization needs to be.
* Per-class AP is where you catch problems a single overall number hides, especially for the classes your project actually depends on.
* Always evaluate on a held-out split that the model never saw during training or internal validation.
