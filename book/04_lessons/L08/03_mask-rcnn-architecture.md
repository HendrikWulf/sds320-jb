---
site:
  outline_maxdepth: 2
---

# Mask R-CNN architecture

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
How Mask R-CNN detects, classifies and masks individual objects
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Frame

You now understand what instance identity means at the data level. This page covers how {term}`Mask R-CNN` actually produces it, building directly on the {term}`Faster R-CNN` architecture from L06. If any of the [L06 detection-architecture](https://hendrikwulf.github.io/sds320-jb/book/lessons/l06/detection-architectures/#a-two-stage-detectors) vocabulary feels distant, it is worth a quick review before continuing.

---

## 2. Motivation

`geoai`'s instance segmentation functions expose very few parameters directly, `num_classes`, `num_channels`, `instance_labels`, and a handful of training settings, which makes the architecture itself feel like a black box unless you know roughly what happens inside. Understanding the four main components here is what lets you make sense of training behavior, loss curves, and failure modes on the following pages, without needing to implement any of it yourself.

---

## 3. Core idea

Mask R-CNN extends {term}`Faster R-CNN` by adding one more parallel branch: alongside the existing class label and bounding box outputs, a new branch predicts a pixel-level mask for each detected object. Everything Faster R-CNN already does, proposing regions and classifying them, stays the same; instance segmentation is what you get by asking the same detector to also answer "and what is this object's exact shape."

---

## 4. Core architecture and components

Mask R-CNN extends object detection into instance segmentation by adding a dedicated mask branch alongside traditional bounding box detection. To understand how an input satellite image becomes a set of individually segmented features, follow the end-to-end processing pipeline below:

```text
input image (e.g., 4-band RGB + NIR)
       ↓
backbone + Feature Pyramid Network (FPN)
       ↓
multiscale feature maps
       ↓
Region Proposal Network (RPN)
       ↓
candidate object regions (RoIs)
       ↓
RoI Align (bilinear interpolation)
       ↓
 ┌──────────────┬──────────────┐
 ↓              ↓
detection head  mask head
 ↓              ↓
class + box     28×28 binary mask
 └───────┬──────┘
         ↓
one complete prediction per instance

```

---

### A. Backbone and Feature Pyramids

The {term}`Backbone` (typically ResNet-50 or ResNet-101) converts raw image pixels into rich, learned feature maps. Rather than analyzing pixel values directly, deeper convolutional layers extract increasingly abstract visual patterns such as edges, textures, and object structures.

* **Multispectral Adaptation:** For four-band Sentinel-2 imagery (RGB + Near-Infrared), the first convolutional layer is modified to accept four input channels instead of three, matching the encoder adjustments used in semantic segmentation.
* **Feature Pyramid Network (FPN):** Real-world spatial features vary significantly in scale—a single satellite scene may contain small, medium, and large agricultural parcels. The FPN extracts features across multiple depth levels of the backbone, providing high-resolution features for small objects and high-level contextual features for large objects.

---

### B. Region Proposal Network (RPN)

Inherited directly from Faster R-CNN, the {term}`Region Proposal Network (RPN)` scans the multiscale feature maps to answer a single question: *Where might an object be located?*

1. **Candidate Generation:** The RPN slides across feature maps and evaluates predefined anchor boxes of different sizes and aspect ratios.
2. **Scoring and Refinement:** For each anchor, it estimates an "objectness" score (the likelihood that a feature is present) and proposes an initial, coarse bounding box.
3. **Filtering:** Proposals with low objectness scores are dropped. Overlapping candidates are filtered using {term}`non-maximum suppression <Non-Maximum Suppression>` (NMS) so that only top-scoring, distinct regions move forward.

---

### C. RoI Align: spatial precision for masks

Before candidate regions pass to the prediction heads, each proposed region of interest (RoI) must be extracted from the feature map and converted into a standard, fixed-size matrix.

```text
candidate bounding box
         ↓
sample precisely via bilinear interpolation (RoI Align)
         ↓
fixed-size feature matrix

```

* **The Problem with RoI Pooling:** Older detection networks used *RoI Pooling*, which rounded floating-point proposal coordinates to the nearest feature grid cell. This introduced small quantization errors of 1 to 2 pixels.
* **Why Spatial Precision Matters:** While a 2-pixel shift has virtually no effect on predicting an object's class label, it significantly degrades pixel-level mask boundaries.
* **The Solution:** **{term}`RoI Align`** eliminates coordinate rounding entirely. It uses bilinear interpolation to sample feature values at continuous coordinates, preserving exact spatial alignment between the original image and the extracted feature map.

```{admonition} Why this matters for small fields
:class: note
A small agricultural field or building footprint might occupy only a few dozen pixels in a training chip. At that scale, coordinate quantization errors can displace mask boundaries by a substantial fraction of the object's total area. RoI Align is essential for generating usable spatial vectors from small features.

```

---

### D. Detection head

Extracted RoI features pass into the **detection head**, which performs two standard object-detection tasks in parallel:

* **Classification:** Predicts the semantic category of the object (e.g., distinguishing an agricultural `field` from `background`).
* **Box Regression:** Refines the initial RPN bounding box to fit tightly around the object boundaries.

This branch operates identically to Faster R-CNN and determines *what* the object is and *where* its overall envelope lies.

---

### E. Mask head

The **mask head** is the defining addition of Mask R-CNN. While the detection head processes bounding boxes, the mask head branch uses a small Fully Convolutional Network (FCN) to predict an exact pixel geometry for each detected object.

* **Binary Representation:** For every candidate region, the mask head outputs a small, fixed-size binary grid (typically $28 \times 28$ pixels) where pixels are classified as either target object (`1`) or background (`0`).
* **Decoupled Predictions:** Because mask prediction is separate from classification, the model avoids competition between learning class labels and learning exact shapes.
* **Final Instance Output:** The $28 \times 28$ mask is scaled up and projected back to its real-world position inside the refined bounding box, yielding four core outputs for every instance:

```text
Instance Output
├── Class Label (e.g., field = 1)
├── Confidence Score (e.g., 0.94)
├── Bounding Box ([xmin, ymin, xmax, ymax])
└── Pixel Mask (binary footprint)

```

---

### F. Detection quality affects mask quality

Mask R-CNN relies on a **top-down** sequence:

```text
detect candidate object (box)
         ↓
segment target object (mask)

```

Because segmentation happens *inside* proposed bounding boxes, errors in the detection stage propagate directly into the final segmentation output:

* **Missed Detections:** If the RPN or detection head fails to propose a candidate box, the mask branch never executes, creating a missed instance ({term}`False Negative`).
* **Merged Objects:** If two touching agricultural fields are bounded inside a single proposed box, the mask head will attempt to segment them as one combined polygon.
* **Truncated Masks:** If a bounding box is too small, the predicted mask will be clipped along the box edges.

```{important}
Mask prediction cannot fix an underlying detection failure. Evaluating instance segmentation requires inspecting both bounding box quality and mask geometry.

```

---

### G. Multi-task loss functions

Mask R-CNN trains all components simultaneously using a combined multi-task loss function ($L_{\text{total}}$):

$$L_{\text{total}} = L_{\text{rpn\_cls}} + L_{\text{rpn\_box}} + L_{\text{cls}} + L_{\text{box}} + L_{\text{mask}}$$

During model training, monitoring individual loss terms helps pinpoint specific weaknesses:

* High $L_{\text{rpn}}$ or $L_{\text{cls}}$ indicates the model struggles to locate or identify candidate objects.
* High $L_{\text{box}}$ indicates imprecise bounding box boundaries.
* High $L_{\text{mask}}$ indicates difficulty resolving exact internal pixel shapes.

---

### H. Model alternatives and trade-offs

While Mask R-CNN is a standard baseline for two-stage instance segmentation, other architecture families address different operational priorities:

:::{dropdown} Other model families
:class: note

* **Two-Stage Models (e.g., Mask R-CNN):** Isolate detection before mask prediction. They offer strong boundary accuracy and robust performance on small features, but require higher computational time.
* **Single-Stage Models (e.g., YOLO-Seg):** Predict bounding boxes and mask coefficients simultaneously in a single pass. They offer significantly faster inference speeds, making them ideal for real-time applications or massive scene processing, though occasionally at the expense of fine boundary detail.
* **Transformer-Based Models (e.g., Mask2Former):** Replace region proposals with learned object queries and attention mechanisms. They treat semantic, instance, and panoptic segmentation under a single framework, excelling at complex scenes with overlapping objects.

Mask R-CNN remains an ideal learning architecture because its modular structure clearly illustrates how detection and segmentation components interact.
:::

---

## 5. Putting the pipeline together

When Mask R-CNN processes an image, the backbone extracts features, the RPN proposes candidate regions, RoI Align samples each region precisely, the detection head classifies and refines each box, and the mask head predicts a shape for each detected object in parallel. After non-maximum suppression removes duplicates, the final output for each detected instance is a class label, a confidence score, a bounding box, and a pixel-level mask, everything needed to convert predictions into georeferenced vector features on a later page.

---

## 6. Common pitfalls

* **Treating the mask head as something you need to configure in detail.** `geoai` handles its internals; your job is choosing sensible high-level parameters (covered on the training page), not designing the mask branch yourself.
* **Forgetting that non-maximum suppression still applies.** Instance segmentation still needs to remove duplicate, overlapping detections of the same object, exactly as detection did in L06.
* **Underestimating RoI Align's practical impact.** It is easy to skip over as a technical footnote, but for small objects, it is a meaningful factor in whether your final boundaries are usable.

---

## 7. Mini task

A colleague says, "Mask R-CNN is just Faster R-CNN with an extra step at the end that draws the mask." Identify what this description gets right and what it oversimplifies.

:::{dropdown} Sample solution
:class: note

It is right that Mask R-CNN keeps Faster R-CNN's backbone, RPN, and detection head essentially unchanged, and that the mask head is the genuinely new addition. It oversimplifies by implying the mask is just a final drawing step tacked onto an existing box: the mask head runs as its own parallel branch, and RoI Align, itself a precision upgrade over the original architecture, is what makes the resulting masks usable at pixel-level accuracy in the first place, not merely a supporting detail.
:::

---

## 8. Key takeaways

* Mask R-CNN extends Faster R-CNN by adding a parallel mask head; the backbone, RPN, and detection head are essentially unchanged from L06's detection architecture.
* The mask head predicts a small, fixed-size binary mask per detected object, resized to fit its bounding box.
* RoI Align replaces the older RoI Pooling operation, removing quantization error through bilinear interpolation, which matters especially for small objects.
* The final output per detected instance combines a class label, confidence score, bounding box, and pixel-level mask.

### Further reading

* ArcGIS API for Python, ["How Mask R-CNN Works?"](https://developers.arcgis.com/python/latest/guide/how-maskrcnn-works/) — a different library's instance-segmentation task documentation, useful for comparison.
* Ultralytics, ["Instance Segmentation"](https://docs.ultralytics.com/tasks/segment) — a different library's instance-segmentation task documentation, useful for comparison.
* Encord, ["Instance Segmentation: Guide 2025"](https://encord.com/blog/instance-segmentation-guide-computer-vision/) — a broader computer-vision perspective on instance segmentation architectures.
