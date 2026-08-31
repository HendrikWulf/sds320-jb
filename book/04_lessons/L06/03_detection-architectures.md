---
site:
  outline_maxdepth: 2
---

# Detector architectures

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Four families of detectors, and how to choose between them for a project.
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this comparison matters

The `geoai` package supports several detection architectures through a single `model_name` parameter, so switching architectures in code is easy. Selecting the right architecture involves matching its strengths to your project's constraints, such as the required level of accuracy, the speed of inference, the size of your objects, and the amount of labelled data available.

---

## 2. Core idea

Detection architectures differ mainly in *how* they go from an input image to a set of boxes. Four broad families are relevant here:

- **Two-stage detectors** first propose candidate regions, then classify and refine each one.
- **Single-stage detectors** predict boxes and classes directly, in a single pass over the image.
- **Transformer-based detectors** treat detection as predicting a fixed-size set of objects directly, without anchors or non-maximum suppression.
- **Zero-shot detectors** find objects described by a text prompt, without any task-specific training data at all.

Each family makes a different trade-off between accuracy, speed, and how much labeled data it needs.

---

## 3. The four families

### A. Two-stage detectors

Two-stage detectors split detection into proposal generation and classification. {term}`Faster R-CNN` is the most influential architecture in this family. A {term}`Region Proposal Network (RPN)` first scans the {term}`backbone <Backbone>`'s feature map and proposes candidate regions that likely contain *some* object, regardless of class. Each proposed region is then cropped, resized to a fixed size, and passed through classification and box-refinement heads.

Conceptually:

```text
image
  ↓
feature extraction
  ↓
candidate regions
  ↓
classify + refine boxes
  ↓
detections
```

This two-step process concentrates computation on promising regions rather than the whole image, which generally gives strong accuracy and handles objects of very different sizes well, at the cost of being slower than single-stage alternatives. `geoai` uses `fasterrcnn_resnet50_fpn_v2` as its default detection model, combining Faster R-CNN with a {term}`Feature Pyramid Network (FPN)` for multi-scale detection.

### B. Single-stage detectors

Single-stage detectors predict boxes and classes directly from the feature map in one pass, which makes them considerably faster, often at a modest cost in accuracy.

Conceptually:

```text
image
  ↓
feature extraction
  ↓
dense predictions
  ↓
detections
```

- **{term}`YOLO`** (You Only Look Once) is the most widely used single-stage family. Modern versions use multi-scale feature pyramids and refined prediction heads, and are competitive with two-stage detectors on many benchmarks while running much faster.
- **{term}`RetinaNet`** addresses a class-imbalance problem common in single-stage detection, where most anchor positions correspond to background, through a loss function that down-weights easy negatives. `geoai` supports it as `retinanet_resnet50_fpn_v2`.
- **{term}`FCOS (Fully Convolutional One-Stage)`** is anchor-free: it predicts boxes directly at each spatial location instead of adjusting pre-defined anchors, removing the need to tune anchor sizes and ratios. `geoai` supports it as `fcos_resnet50_fpn`.

### C. Transformer-based detectors

{term}`DETR` (DEtection TRansformer) reframes detection as predicting a fixed-size set of objects in parallel, using a transformer encoder-decoder. The encoder uses self-attention to capture context across the whole image, which is useful for understanding spatial relationships in overhead imagery. The decoder turns a set of learned "object queries" into detections directly, without anchor boxes or {term}`non-maximum suppression <Non-Maximum Suppression (NMS)>`.

Conceptually:

```text
image features
      ↓
transformer
      ↓
object queries
      ↓
set of predictions
```

DETR's main advantages are architectural simplicity and global context reasoning; its main drawbacks are slower training convergence and, in the original version, weaker performance on very small objects. Newer variants such as Deformable DETR, DINO, and RT-DETR address many of these limitations, and real-time transformer detectors continue to develop quickly (see Further reading for a current example built on the same benchmark dataset used later in this lesson).

### D. Zero-shot detectors

Zero-shot detectors can find objects described by a text prompt, without task-specific training data. {term}`Zero-Shot Object Detection` models such as **OWL-ViT** combine a vision transformer with a text encoder, so you can ask for "solar panel" or "construction crane" directly in natural language. **Grounding DINO** extends this idea further and can approach the accuracy of supervised, task-specific detectors in some settings.

Zero-shot detectors typically trail dedicated, trained detectors in accuracy, but they are valuable for rapid prototyping: scanning imagery for a new object class before you have invested in any labeled training data at all.

However, a text prompt does not remove the need for validation. Domain shifts in viewing angle, image resolution, sensor characteristics and geographic context can strongly affect results.

For a project that ultimately requires dependable quantitative outputs, a task-specific detector trained or fine-tuned on suitable data may still be preferable.

---

## 4. Comparing the families

| Family | Examples | Main strength | Main trade-off | Good starting point when... |
| --- | --- | --- | --- | --- |
| Two-stage | Faster R-CNN | Strong localization baseline | Usually slower | accuracy is the main priority |
| Single-stage | YOLO, RetinaNet, FCOS | Efficient prediction | trade-offs vary by model | many images must be processed |
| Transformer-based | DETR family | Global context and set prediction | training and small-object behaviour vary | you want a transformer workflow |
| Zero-shot | OWL-ViT, Grounding DINO | No project-specific labels needed for initial exploration | performance can vary strongly by domain | you are testing feasibility |

This table is a starting point, not a ranking.

Beyond this table, a practical rule of thumb: check the resolution of your imagery against the size of your target objects before picking an architecture. Objects spanning only a few pixels (vehicles in 1-meter imagery) need strong small-object handling, which is exactly what a Feature Pyramid Network provides. Objects that are large relative to your resolution (buildings in 30-centimeter aerial imagery) are comfortably handled by nearly any of the architectures above. In practice, many projects start with a pre-trained or zero-shot model to check feasibility quickly, then move to a task-specific detector, such as the Faster R-CNN workflow on the next page, once the approach is validated.

```{tip}
Matching an architecture's design strengths to the scale of your target objects usually matters more than chasing the newest or highest-benchmark architecture. A well-matched, slightly older architecture will often outperform a mismatched, state-of-the-art one on your specific imagery.
```

## 5. What `geoai` supports

The source workflow exposes several model choices through the `model_name` argument of `train_multiclass_detector`.

Relevant examples include:

| `model_name` | Type | Comment |
| --- | --- | --- |
| `fasterrcnn_resnet50_fpn_v2` | Two-stage | Main baseline in this lesson |
| `fasterrcnn_mobilenet_v3_large_fpn` | Two-stage | Lighter two-stage option |
| `retinanet_resnet50_fpn_v2` | Single-stage | RetinaNet with FPN |
| `fcos_resnet50_fpn` | Single-stage | Anchor-free |
| `maskrcnn_resnet50_fpn` | Two-stage | Also predicts instance masks |

```{important}
Do not assume that every architecture discussed conceptually can be selected through the same API. [Check the documentation](https://opengeoai.org/object_detect/?h=fasterrcnn_resnet50_fpn_v2#geoai.object_detect.train_multiclass_detector) for the package and version you actually use.
```

The main practical workflow deliberately starts with **Faster R-CNN**. Establishing one understandable baseline is usually more useful than testing many models without a clear comparison plan.

---

## 6. Python activation

Switching architectures in `geoai` is a one-line change: the `model_name` string passed to `train_multiclass_detector()` on the next page. Comparing architectures experimentally can be as simple as looping over a list of `model_name` values and re-running training for each, provided you keep every other parameter fixed for a fair comparison (see the Mini task below).

Starting completely from random model parameters is rarely necessary for a course-scale detection project. A {term}`Pre-Trained Model` provides parameters learned previously from another dataset. {term}`Transfer Learning` or {term}`Fine-Tuning` then adapts that model to your classes.

In the practical workflow, setting:

```python
pretrained=True
```

initializes the model from pre-trained weights. This can make training more feasible when your own labelled dataset is limited.

---

## 7. Common pitfalls

- **Assuming a newer architecture is automatically better for your data.** Benchmark leaderboards reflect performance on specific datasets. Your imagery, object sizes, and class balance may favor a different architecture entirely.
- **Comparing architectures unfairly.** If you change the architecture and the learning rate, batch size, or number of epochs at the same time, you cannot tell which change caused a performance difference. Vary one thing at a time.
- **Choosing a zero-shot detector for a production deliverable.** Zero-shot models are excellent for exploration, but if your project needs reliable, quantifiable accuracy, plan to move to a trained, task-specific detector.

---

## 8. Mini task

Using the table above, pick a `model_name` for each of these two scenarios, and justify your choice in one sentence each:

1. A project counting shipping containers in high-resolution port imagery, where inference will run on a laptop with no GPU and needs to process thousands of tiles.
2. A project detecting small agricultural structures (a few pixels wide) across a large region, where accuracy matters more than speed and a GPU is available.

:::{dropdown} Sample solution
:class: note

1. `fasterrcnn_mobilenet_v3_large_fpn` or `retinanet_resnet50_fpn_v2` — both prioritize inference speed on limited hardware, with the lighter Faster R-CNN variant or a single-stage detector being reasonable choices when GPU access is not guaranteed.
2. `fasterrcnn_resnet50_fpn_v2` — the default two-stage architecture with a Feature Pyramid Network handles small, multi-scale objects well, and the project's stated priorities (accuracy over speed, GPU available) match its trade-offs.
:::

---

## 9. Key takeaways

- Two-stage detectors (Faster R-CNN) prioritize accuracy; single-stage detectors (YOLO, RetinaNet, FCOS) prioritize speed; transformer-based detectors (DETR) simplify the pipeline and add global context; zero-shot detectors (OWL-ViT, Grounding DINO) remove the need for task-specific training data.
- The `geoai` package exposes architecture choice through a single `model_name` string, making experimentation cheap once your data pipeline is set up.
- Matching object size and resolution to an architecture's strengths generally matters more than choosing the newest available model.
- A common project pattern is: prototype with a zero-shot model, then move to a trained detector such as Faster R-CNN once the approach is validated.

### Further reading

- GeoWGS84, ["A Complete Guide to Object Detection with Deep Learning and Machine Learning"](https://www.geowgs84.ai/post/a-complete-guide-to-object-detection-with-deep-learning-and-machine-learning) — a broader survey of algorithms and architectures beyond what is covered here.
- OpenGeoAI, ["RF-DETR Detection"](https://opengeoai.org/examples/rfdetr_detection/) — a worked example of a current real-time, anchor-free transformer detector trained on the same NWPU-VHR-10 dataset used on the next page.
- [Video walkthrough comparing detection architectures](https://youtu.be/ZTXYr29i9Ak)
