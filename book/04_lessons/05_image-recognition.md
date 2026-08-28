---
site:
  outline_maxdepth: 2
---

# L05 – Image recognition

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Classifying image chips into land-use and land-cover classes
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

In [Lesson 04](04_training-data.md), you prepared training data: imagery, labels, chips, quality checks and splits. This lesson uses those ideas in the first modelling workflow of SDS320: {term}`Image Recognition`.

Image recognition asks a simple question of your data: *not* "What is every pixel?" but "What is this whole tile, mostly?". It assigns a single label to an entire image or image chip. In geospatial work, this is often also called image classification or scene classification. A model might classify a chip as forest, residential, industrial, river or crop land.

This makes image recognition a useful first supervised GeoAI task. The output is simpler than object detection or segmentation: one image goes in, one class label comes out. At the same time, the workflow introduces ideas you will reuse in later lessons: model architectures, {term}`Transfer Learning`, training history, test-set evaluation, confusion matrices and visual prediction checks.

---

## 2. Motivation

Image recognition is often the fastest path from an idea to a working model. It needs less annotation effort than segmentation or detection, since a label describes a whole tile rather than every pixel or object within it, and it trains quickly enough to iterate several times in a single class session. For a project-oriented course like SDS320, that speed matters: a working baseline model early in the semester gives you something concrete to improve, rather than a plan you have not yet tested.

Image recognition is useful when one dominant label per tile is enough. It is not the right method when you need the exact position of objects or a pixel-level map. That distinction matters for SDS320 projects because it helps you avoid choosing a method that produces the wrong type of output.

A useful rule for this lesson is:

```text
Use image recognition when the tile-level label is the answer.
```

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- explain when image recognition is appropriate for a GeoAI project,
- describe how an image tile becomes class probabilities and a predicted label,
- compare common classification architectures at a decision-oriented level,
- organise image classification data in ImageFolder format,
- train a first classifier on the EuroSAT RGB dataset with `geoai.recognize`,
- evaluate a classifier with accuracy, precision, recall, F1-score and confusion matrices,
- compare two architectures fairly using the same dataset and hyperparameters.

---

## 4. Lesson roadmap

Work through the pages in this order:

1. [When to use image recognition](L05/01_when-to-use-image-recognition.md) — decide whether tile-level classification fits your question.
2. [From pixels to labels](L05/02_from-pixels-to-labels.md) — understand the input-output logic of an image classifier.
3. [Classification architectures](L05/03_classification-architectures.md) — compare ResNet, EfficientNet, Vision Transformers and ConvNeXt.
4. [ImageFolder and EuroSAT](L05/04_imagefolder-and-eurosat.md) — inspect the dataset structure and visual examples.
5. [Training a classifier](L05/05_training-a-classifier.md) — train a first ResNet-50 classifier with the GeoAI API.
6. [Evaluation and confusion matrices](L05/06_evaluation-and-confusion-matrices.md) — interpret metrics, confusion matrices and visual predictions.
7. [Architecture comparison and reuse](L05/07_architecture-comparison-and-reuse.md) — compare ResNet-50 and EfficientNet-B0 fairly and consider model reuse.
8. [Project transfer](L05/08_project-transfer.md) — decide whether image recognition fits your own SDS320 project.

---

## 5. Project framing

As you work through this lesson, keep your own project in mind.

Use these guiding questions:

- Is one label per image chip enough for my project output?
- What would my classes be, and are they visually separable?
- How large should one chip be so that it contains enough context?
- Which classes do I expect the model to confuse?

```{tip}
Image recognition is often a good baseline. Even if your final project needs detection or segmentation, a simple classifier can help you understand whether the imagery contains separable visual patterns.
```

---

## 6. Before class

Before class, prepare the following:

- [ ] Review your training-data plan from Lesson 04.
- [ ] Check whether your project question needs tile-level labels, object locations or pixel-level maps.
- [ ] Think of one or two candidate class sets for your own project, even rough ones.
- [ ] Skim the [evaluation plan](../03_project/05_data-methods-scope.md) guidance in the Project handbook; this lesson gives you concrete tools for that section.

<!-- TODO: confirm whether all `geoai.recognize` functions used in this lesson are included in the final SDS320 environment. -->

---

## 7. After this lesson

After completing this lesson, you should have:

- a clear understanding of what image recognition can and cannot answer,
- a first trained classifier on EuroSAT RGB,
- a training-history plot,
- a classification report,
- raw and normalised confusion matrices,
- a small set of visual prediction examples,
- a first decision about whether tile-level classification could support your own project.

These outputs do not need to be perfect. Their purpose is to help you understand the classification workflow before moving to methods with more complex spatial outputs.
