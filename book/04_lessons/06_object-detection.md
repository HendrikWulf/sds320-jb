---
site:
  outline_maxdepth: 2
---

# L06 – Object detection

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Locating, classifying and counting individual objects in geospatial imagery
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

In the previous lesson, you used {term}`Image Recognition` to assign a label to an entire image or image chip. This works well when your question is about the overall scene: *What type of land use does this tile represent?* or *Does this image contain a particular feature?*

Many spatial questions require more detail. You may want to know **how many** trees are in a park, **where** individual ships are located in a harbor, or **which** rooftops contain solar panels. One label for the whole image is no longer enough.

{term}`Object Detection` addresses this problem by predicting a variable number of individual objects. Each prediction normally contains:

- a {term}`Bounding Box` describing the object's location,
- a class label describing what the object is,
- a {term}`Confidence Score` describing the model's confidence in that prediction.

Detection therefore adds **object-level location** to image recognition.

Looking ahead, L07 covers semantic segmentation, which labels every pixel but cannot tell two touching objects of the same class apart. L08 combines both ideas into instance segmentation. Object detection sits conceptually between the two: it localizes individual objects without needing to trace their exact outlines.

---

## 2. Motivation

Object detection is particularly useful when your research question concerns **discrete and countable features**.

Examples include:

- counting trees,
- inventorying solar panels,
- locating ships, cars or aircraft,
- identifying individual buildings,
- locating sports facilities,
- screening imagery for infrastructure after a disaster.

For an SDS320 project, the important question is not simply whether object detection is technically possible. You need to decide whether its outputs match your research question.

A rectangular bounding box may be perfectly adequate if you need locations and counts. It may be insufficient if your analysis depends on exact area, shape or boundaries.

Detection also introduces several project decisions that did not occur in the same form during image recognition. You need suitable {term}`Training Data` with object-level annotations, must consider object size relative to {term}`Spatial Resolution`, and need evaluation metrics that assess both classification and localization.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- Decide whether object detection fits a given project question, or whether segmentation is a better match
- Explain bounding boxes, confidence scores, {term}`IoU <Intersection over Union (IoU)>`, non-maximum suppression, and anchor boxes
- Compare two-stage, single-stage, transformer-based, and zero-shot detection architectures and choose one based on project constraints
- Prepare and visually check a detection dataset in {term}`COCO Format`
- Train and evaluate a multi-class detector with the `geoai` package, and interpret {term}`mAP <Mean Average Precision (mAP)>` results, including per-class differences
- Run inference on new imagery, visualize detections, and identify likely false positives and false negatives

---

## 4. Lesson roadmap

Work through the lesson in the following sequence:

1. [When to use detection](L06/01_when-to-use-object-detection.md) — decide whether bounding-box predictions actually answer your research question.
2. [Detection concepts](L06/02_bounding-boxes-and-detection-concepts.md) — understand boxes, confidence scores, IoU, Non-Maximum Suppression and anchor boxes.
3. [Detector architectures](L06/03_detection-architectures.md) — compare two-stage, single-stage, transformer-based and zero-shot approaches.
4. [Detection data](L06/04_detection-datasets-and-annotations.md) — examine annotation formats and prepare the NWPU-VHR-10 benchmark dataset.
5. [Train a detector](L06/05_training-a-detector.md) — train a multi-class Faster R-CNN model with `geoai`.
6. [Evaluate detections](L06/06_evaluation-with-map.md) — interpret precision, recall, AP and mAP at different overlap thresholds.
7. [Inference](L06/07_inference-and-visualisation.md) — apply the trained model, visualise predictions and inspect errors.
8. [Project transfer](L06/08_project-transfer.md) — decide how, or whether, object detection belongs in your own project.

Pages 1–4 build the conceptual and data foundation. Page 5 is the main hands-on workflow. Pages 6–7 close the loop by evaluating and applying the trained model. Page 8 asks you to transfer all of it to your own project.

The red thread is:

```text
Is detection the right task?
        ↓
How are objects represented?
        ↓
Which detector should I start with?
        ↓
Are my annotations suitable?
        ↓
Can I train a reproducible baseline?
        ↓
How well does it work?
        ↓
What happens on new imagery?
        ↓
Does this workflow fit my project?
```

---

## 5. Project framing

While reading, keep your own project in mind.

Ask yourself:

1. **What exactly is one object?**  
   Can you define a target consistently enough that two people would annotate approximately the same objects?

2. **Are the objects visible at your image resolution?**  
   A detector cannot recover reliable object information that is not represented clearly in the input imagery.

3. **Do you need boxes or boundaries?**  
   If location and count are sufficient, detection may be appropriate. If shape or area matters, instance segmentation may be a better fit.

4. **How would you know whether your detector is good enough?**  
   Your {term}`Evaluation Metric` and acceptable error rates should follow from the project question rather than from a benchmark alone.

```{important}
A technically impressive detector is not automatically a useful spatial analysis. Your method, data resolution, labels, evaluation and final interpretation all need to match the research question.
```

---

## 6. Before class

Before the practical part of the lesson:

- [ ] Read the first four pages up to **Detection data**.
- [ ] Identify one discrete object that could plausibly occur in your project imagery.
- [ ] Estimate how large that object is relative to the image pixels.
- [ ] Check that your SDS320 Python environment can import `geoai`.
- [ ] If you plan to train during class, make sure you have access to the course computing environment or a machine suitable for deep-learning experiments.
- [ ] Optional: use one of the provided overview resources to review the basic idea of object detection.

Optional background:

- [IBM: Object detection](https://www.ibm.com/think/topics/object-detection#691946469)
- [GeoWGS84: Guide to object detection](https://www.geowgs84.ai/post/a-complete-guide-to-object-detection-with-deep-learning-and-machine-learning)
- [Video introduction 1](https://youtu.be/92AtjfA6Jgs?si=walq5xQWU5EkyC72)
- [Video introduction 2](https://youtu.be/ZTXYr29i9Ak?si=0FV4JKW8dhdUR0Uf)

---

## 7. After this lesson

By the end of the lesson, you should have more than a trained example model.

For your own project, you should be able to state:

- whether object detection fits your research question,
- which objects you would detect,
- what imagery and spatial resolution you need,
- how the objects would be annotated,
- which architecture provides a reasonable baseline,
- which metrics you would use,
- how you would inspect errors,
- which limitations could prevent reliable use of the detections.
