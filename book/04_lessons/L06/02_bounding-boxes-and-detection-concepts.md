---
site:
  outline_maxdepth: 2
---

# Detection concepts

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Understand how a detector represents, scores and filters object predictions
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why these concepts matter

Every detection architecture in the next page, whether Faster R-CNN, YOLO, or DETR, is built around these same five ideas. Once you understand them here, comparing architectures and reading `geoai` output (training curves, evaluation tables, visualized detections) becomes far easier, because you will recognize what each number and each colored box actually represents.

---

## 2. Core idea

A detection model does not output a single answer. For one input image, it outputs a *set* of predictions, each one a {term}`bounding box <Bounding Box>` (a rectangle marking where an object is), a class label, and a {term}`confidence score <Confidence Score>` (how sure the model is). Two supporting mechanisms, {term}`IoU <Intersection over Union (IoU)>` and non-maximum suppression, decide which of the model's raw, often overlapping, box proposals actually survive into the final output. Anchor boxes are a design choice some architectures use to make that raw prediction problem easier to learn in the first place.

---

## 3. Building blocks

### A. Bounding boxes

A bounding box is defined by four numbers. The two common conventions are:

- **Corner format**: `(x_min, y_min, x_max, y_max)`, the top-left and bottom-right corners.
- **Center format**: `(center_x, center_y, width, height)`.

Both describe the same rectangle. Different annotation formats and libraries use different conventions, so always check the expected representation.
In a geospatial workflow, these coordinates start out in pixel space, and are only meaningful for GIS work once converted to geographic coordinates using the image's affine transform, the same transform you already used in L03 and L04 to relate pixel positions to real-world locations.

### B. Confidence scores & thresholds

Each box also carries a {term}`Confidence Score`, a value between 0 and 1. A higher score means that the model assigns greater confidence to that prediction. It does **not** guarantee that the detection is correct.

During inference, you normally choose a **confidence threshold** to filter out low-quality predictions: a high threshold keeps only the model's most certain detections (fewer false positives, but you may miss some real objects), while a low threshold keeps more detections (higher recall, but more clutter). You will tune this threshold directly when running inference in a later page.

For example:

```text
threshold = 0.80 → keep only high-confidence predictions
threshold = 0.50 → keep more predictions
threshold = 0.30 → keep many uncertain predictions
```

The choice creates a trade-off:

| Threshold | Likely effect |
| --- | --- |
| Higher | Fewer predictions, usually fewer false positives, potentially more missed objects |
| Lower | More predictions, potentially higher recall, but usually more false positives |

There is no universally correct threshold. The appropriate value depends on the cost of different errors in your project.

### C. Intersection over Union (IoU)

How can we measure whether two boxes describe approximately the same location? {term}`Intersection over Union (IoU)` compares their overlap: the area where they intersect, divided by the area they cover together (their union). An IoU of 1.0 means the boxes align perfectly; an IoU of 0 means they do not overlap at all.

```{code-cell} python
def iou(box_a, box_b):
    """Compute IoU for two boxes in (x_min, y_min, x_max, y_max) format."""
    x_min = max(box_a[0], box_b[0])
    y_min = max(box_a[1], box_b[1])
    x_max = min(box_a[2], box_b[2])
    y_max = min(box_a[3], box_b[3])

    intersection = max(0, x_max - x_min) * max(0, y_max - y_min)
    area_a = (box_a[2] - box_a[0]) * (box_a[3] - box_a[1])
    area_b = (box_b[2] - box_b[0]) * (box_b[3] - box_b[1])

    return intersection / (area_a + area_b - intersection)


predicted_box = (50, 50, 150, 150)
ground_truth_box = (70, 70, 160, 160)
print(f"IoU: {iou(predicted_box, ground_truth_box):.2f}")
```

IoU has two jobs in this lesson: during training, it helps the model decide which raw predictions correspond to which ground-truth objects, and during evaluation (the mAP page), it helps removing duplicate predictions. Keep their roles separate when documenting your workflow.

### D. Non-maximum suppression (NMS)

Detection models often produce several overlapping boxes for the same physical object, especially in dense scenes like a parking lot or a shipping harbor. {term}`Non-Maximum Suppression` ({abbr}`NMS (Non-Maximum Suppression)`) removes this redundancy: for each class, it keeps the highest-confidence box and removes any other box that overlaps it beyond an IoU threshold, typically applied per class so a car and an overlapping tree box are not accidentally merged.

The result should contain fewer redundant boxes.

```text
before NMS                         after NMS

┌──────────┐                      ┌──────────┐
│ ┌──────────┐                    │          │
│ │ object │ │  two boxes   →     │ object   │  one box
└─│────────┘ │                    │          │
  └──────────┘                    └──────────┘
```

```{admonition} Where you will see this in practice
:class: note
The `geoai` inference functions used later in this lesson (`multiclass_detection`) apply non-maximum suppression automatically, including across the overlapping tile edges produced by tiled inference. You do not need to implement it yourself, but understanding what it does will help you interpret the "before NMS" and "after NMS" detection counts printed during inference.
```

### E. Anchor boxes

Many detectors use {term}`Anchor Boxes <Anchor box>` templates as starting points for predicting object locations. Instead of learning every box from scratch, the model begins with boxes of different sizes, aspect ratios, and positions. It then predicts adjustments to these templates. This is useful because objects in imagery occur at different shapes and scales.

But not all modern detectors use anchors. Anchor-free approaches such as FCOS and transformer-based approaches such as DETR use different strategies. You therefore need to understand anchor boxes conceptually, but you will not manually configure them in the main practical workflow.

## 4. Common pitfalls

- **Confusing confidence score with correctness.** A high confidence score means the model is sure, not that the prediction is right. Always sanity-check detections visually before trusting them for a project deliverable.
- **Picking a confidence threshold without testing it.** The "right" threshold depends on whether false positives or false negatives are more costly for your application. Test a few values rather than keeping a default.
- **Forgetting that IoU is direction-agnostic.** IoU treats a predicted box and a ground-truth box symmetrically. It tells you how well two boxes overlap, not which one is "more correct."

---

## 5. Mini task

Using the `iou()` function above, compute the IoU for `predicted_box = (0, 0, 100, 100)` and `ground_truth_box = (50, 0, 150, 100)` (two boxes of equal size, overlapping by half their width). Before running it, predict whether the IoU will be above or below 0.5.

:::{dropdown} Sample solution
:class: note

```{code-cell} python
predicted_box = (0, 0, 100, 100)
ground_truth_box = (50, 0, 150, 100)
print(f"IoU: {iou(predicted_box, ground_truth_box):.2f}")
```

The two boxes overlap in a 50 by 100 region (area 5,000), while their union covers 15,000 square units, giving an IoU of about 0.33. This is a useful benchmark to remember: two same-sized boxes overlapping by half their width already fall below the common 0.5 IoU threshold used in evaluation, which shows how strict that threshold really is.
:::

---

## 6. Key takeaways

- A detection model outputs a set of bounding boxes, each with a class label and a confidence score, not a single prediction.
- IoU measures the overlap between two boxes and underlies both training (matching predictions to ground truth) and evaluation (deciding if a detection is correct).
- Non-maximum suppression removes redundant, overlapping detections of the same object, keeping only the highest-confidence box per class.
- Anchor boxes give some architectures a set of starting templates to adjust, rather than predicting coordinates from nothing; not all architectures use them.
- The confidence threshold you choose at inference time trades off false positives against false negatives, and should be a deliberate project decision, not a leftover default.

---

## 7. Further reading

- [Video walkthrough of core object detection concepts](https://youtu.be/92AtjfA6Jgs) — a supplementary visual walkthrough of an object detection workflow
