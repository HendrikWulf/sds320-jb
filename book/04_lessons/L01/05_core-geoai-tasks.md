---
site:
  outline_maxdepth: 2
---

# Core GeoAI tasks

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A map of the task families covered later in this book
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why a task taxonomy

Once you know an application area, the next question is: what kind of output do I actually need — a box around each object, a class label per pixel, a predicted number, or something else? This page previews the core {term}`GeoAI` task families this book covers from [L06](../06_object-detection.md) onward, so you can recognise which one matches your project idea early.

---

## 2. The task families at a glance

| Task family | Question it answers | Typical output | Covered in |
| --- | --- | --- | --- |
| {term}`Object Detection` | Where are individual instances of X? | Bounding boxes with class labels | [L06](../06_object-detection.md) |
| {term}`Semantic Segmentation` | Which class does each pixel belong to? | A class label per pixel ({term}`Mask`) | [L07](../07_semantic-segmentation.md) |
| {term}`Instance Segmentation` | Where is each individual object, pixel by pixel? | A separate mask per object instance | [L08](../08_instance-segmentation.md) |
| {term}`Image Translation` | Can I transform this image into another representation? | A generated or enhanced image | [L09](../09_image-translation.md) |
| {term}`Change Detection` | What changed between two time points? | A change mask or change map | [L10](../10_change-detection.md) |
| {term}`Regression` | What is the value of a continuous variable here? | A predicted number per pixel or per area | [L11](../11_regression.md) |
| Foundation-model segmentation | Can I segment without training a task-specific model? | Prompt-based masks | [L12 – Segment Anything](../12_segment-anything.md) |
| {term}`Satellite Embedding` | Can I compare or cluster locations by learned similarity? | A vector representation per location | [L13](../13_satellite-embeddings.md) |

---

## 3. Detection versus segmentation

Detection and segmentation are the two task families most often confused. The distinction matters because it changes what {term}`Training Data` you need and how you evaluate the result:

* **Object detection** answers "where are the buildings" with a box around each one — useful when you need to count or locate discrete objects, and boxes are cheaper to label than pixel-precise outlines.
* **Semantic segmentation** answers "which pixels are building" without distinguishing individual buildings — useful for area-based questions like "how much of this scene is built-up".
* **Instance segmentation** combines both: it distinguishes individual objects *and* outlines them pixel by pixel — the most detailed (and most labelling-expensive) of the three.

```{tip}
If your project question is "how many" or "where exactly is one specific object", think detection or instance segmentation. If it is "how much area" or "what fraction", think semantic segmentation.
```

---

## 4. Change, translation, and regression

The remaining task families answer different kinds of questions:

* **Change detection** compares two (or more) points in time over the same area, and is central to monitoring applications such as deforestation or urban growth.
* **Image translation** transforms an image into another useful representation — for example {term}`Super-Resolution`, turning a coarser image into a sharper one, or converting between sensor types.
* **Regression** predicts a continuous value rather than a class — canopy height, crop yield, or population density are typical examples.

---

## 5. Foundation models as a cross-cutting layer

{term}`Foundation Model`-based approaches, covered in the [next page](06_foundation-models.md) and in depth in [L12](../12_segment-anything.md) and [L13](../13_satellite-embeddings.md), do not fit neatly into a single row of the table above. They can support detection, segmentation, or similarity-based tasks without always requiring you to train a model from scratch — which is why they are introduced separately rather than as one more task family.

---

## 6. Key takeaways

* This book's modelling lessons (L06–L13) each correspond to one core GeoAI task family.
* Object detection, semantic segmentation, and instance segmentation answer related but distinct questions and need different training data.
* Change detection, image translation, and regression each solve a different kind of spatial question beyond classification.
* Foundation models cut across these families rather than forming their own row — more on this on the [next page](06_foundation-models.md).
