# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning this lesson into concrete progress on your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this page matters

This lesson covered a lot of ground: when detection fits, the concepts behind it, four architecture families, dataset formats, a full training and evaluation workflow, and inference. This page is not about learning anything new. It is about deciding, concretely, whether object detection belongs in your SDS320 project, and if so, sketching the specific choices, target objects, annotation approach, architecture, and evaluation plan, that your project will need.

---

## 2. Project checklist

Work through this list for your own project:

- [ ] I can name the specific discrete object(s) my project needs to find, count, or locate.
- [ ] I have checked my target object size against my available imagery's spatial resolution.
- [ ] I know whether I need bounding boxes only, or whether I will eventually need exact object boundaries (which would point toward instance segmentation in L08 instead).
- [ ] I have identified where my training annotations will come from: an existing benchmark, a public dataset, or annotations I will create myself.
- [ ] I have a first-pass idea of which `geoai` architecture (`model_name`) fits my accuracy, speed, and data constraints.
- [ ] I know which evaluation metric (mAP@0.5, mAP@0.75, or mAP@[0.5:0.95]) matches how precise my project's localization needs to be.
- [ ] I have thought about a reasonable confidence-threshold strategy for my intended use case.

---

## 3. Decision points

**Detection, or a different task?** If your objects are discrete and countable, and approximate location is enough, detection fits. If you need exact boundaries or area, revisit segmentation (L07) or instance segmentation (L08) instead. If you are unsure, look back at the mini task on the "When Detection Fits" page.

**Which annotation source?** If a public benchmark or dataset already contains your object classes, reuse it, as this lesson did with NWPU-VHR-10, and save significant annotation effort. If not, plan realistically for how many bounding boxes you can draw yourself in the time available; detection annotation is faster than segmentation masks, but it still takes real time per object.

**Which architecture?** Use the decision table from the architectures page as a starting point. A reasonable default for most course projects is `fasterrcnn_resnet50_fpn_v2`, the same architecture used in this lesson's workflow, unless your project has a specific reason to prioritize speed (single-stage) or has essentially no labeled data yet (zero-shot, as an exploratory first step).

**Which evaluation metric to report?** Choose based on what your project deliverable actually needs. A vehicle count for a traffic study can reasonably rely on mAP@0.5. A detection feeding into precise GIS measurements should be evaluated and reported at a stricter threshold.

**What confidence threshold to ship with?** Decide, explicitly, whether missed objects or false alarms are more costly for your use case, and set your default threshold accordingly, rather than leaving it at 0.5 by default without discussion.

---

## 4. Common pitfalls

- **Scoping a detection task around objects that are too small for available imagery.** Check object size against resolution before committing; this is the single most common way an otherwise reasonable detection project underperforms.
- **Planning to annotate more objects than are actually feasible in the time available.** Detection annotation is faster than segmentation, but drawing hundreds of accurate boxes still takes real hours. Scope your annotation plan realistically.
- **Reporting only an overall mAP in your project write-up.** Reviewers (and you, later) will want to know how the model performs on the classes that actually matter for your question, not just the average.
- **Treating a zero-shot prototype as a finished result.** Zero-shot detection is excellent for early feasibility checks, but a course project deliverable should generally rest on a trained, evaluated, task-specific detector.

---

## 5. Mini deliverable

Produce a short detection project plan (half a page is enough) that states:

1. Your target object class(es) and why they matter for your project question.
2. Your annotation source (existing dataset, or your own annotation plan with an estimated time budget).
3. Your planned starting architecture (`model_name`) and a one-sentence justification.
4. Your primary evaluation metric and confidence-threshold strategy.
5. One likely failure mode you anticipate (for example, a specific class you expect to be hard to detect), based on what you now know from this lesson.

---

## 6. Reflection questions

1. Does your project genuinely need object-level localization, or would a simpler classification approach (L05) actually answer your question?
2. If your target objects are small relative to your imagery's resolution, what does that imply for your architecture choice and your expectations for detection quality?
3. How will you decide, and justify, the confidence threshold you ship with?
4. If your per-class AP turns out to be uneven across classes, as it was for several classes in this lesson's example, how will you report that honestly in your project write-up?
5. Would your project be better served by detection, or does it actually belong to the instance segmentation task covered in L08?
