---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning this lesson into concrete progress on your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

This lesson covered a lot: when segmentation fits, how encoder-decoder architectures work, a full binary workflow, band and sensor comparisons, pre-trained models, and multi-class post-processing. This page is about deciding, concretely, whether semantic segmentation belongs in your SDS320 project, and sketching the specific choices, classes, bands, architecture, and evaluation plan, your project will need.

---

## 2. Project checklist

Work through this list for your own project:

- [ ] I can name the specific class(es) my project needs to map, and whether the task is binary or multi-class.
- [ ] I have decided whether my target is genuinely area-like (segmentation) or actually a discrete-object counting problem (better suited to L06 or L08).
- [ ] I know which bands my available imagery provides, and whether additional spectral information (NIR, SWIR) would plausibly help my target class.
- [ ] I have identified where my training labels will come from: an existing dataset, a pre-trained model, or labels I will create myself.
- [ ] I have a first-pass architecture and encoder choice (`unet` with a ResNet encoder is a reasonable default).
- [ ] I know which evaluation metrics (IoU, F1, precision, recall) matter most for my specific classes, including any rare or minority classes.
- [ ] I have a plan for post-processing: whether I need vector output, and whether smoothing or orthogonalization fits my target's shape.

---

## 3. Decision points

**Segmentation, or a different task?** If your target is area-like or continuous, segmentation fits. If you actually need to count or separate individual instances, revisit L06 (detection) or look ahead to L08 (instance segmentation). If you are unsure, revisit the mini task on the "When Segmentation Fits" page.

**Binary or multi-class?** Start binary if your project genuinely only needs one target class versus everything else. Move to multi-class only when your project actually needs to distinguish among several specific categories; more classes mean more labeled data and training time needed to reach a comparable quality per class.

**Custom training, or a pre-trained model?** If a sensor-agnostic pre-trained model (like the water and cloud examples in this lesson) already covers your target class and sensor, it can save substantial time. If your target class is project-specific (a particular crop type, a local land-use category), custom training is likely unavoidable.

**Which architecture and encoder?** `unet` with `resnet34` is a reasonable default for most course projects. Only move to an alternative architecture or encoder once you have a specific, diagnosed limitation, not by default.

**What post-processing does your deliverable actually need?** A raster mask may be sufficient for visual inspection or area statistics; a GIS deliverable that needs to be joined, filtered, or shared as discrete features needs vectorization, and possibly smoothing or orthogonalization depending on whether your target has natural curves or right angles.

---

## 4. Common pitfalls

- **Scoping a segmentation task around a class with too little labeled data.** Segmentation labels are more expensive to produce than classification or even detection labels; be realistic about how much labeled area you can actually create or find.
- **Choosing multi-class before establishing a working binary baseline.** A binary version of your problem is a useful sanity check before adding the complexity of many classes at once.
- **Ignoring class imbalance in a multi-class scheme.** If you know some classes will be rare, plan for it (class-weighted loss, oversampling) rather than discovering the problem after a full training run.
- **Reporting only an aggregate IoU or F1 in your project write-up.** As emphasized throughout this lesson, per-class results are what reveal whether your specific classes of interest actually perform well.

---

## 5. Mini deliverable

Produce a short segmentation project plan (half a page is enough) that states:

1. Your target class(es), and whether the task is binary or multi-class.
2. Your imagery source and available bands, and whether you plan to use all of them.
3. Your label source (existing dataset, pre-trained model, or your own annotation plan with a time estimate).
4. Your planned starting architecture and encoder, with a one-sentence justification.
5. Your primary evaluation metric(s) and your plan for handling any class imbalance.
6. Your intended output format (raster mask, or vector polygons with specific post-processing).

---

## 6. Reflection questions

1. Does your project genuinely need pixel-level masks, or would a simpler classification (L05) or detection (L06) approach actually answer your question?
2. If your target class has a distinctive spectral signature (like water's NIR/SWIR absorption), what does that imply for which bands you should prioritize?
3. Would a pre-trained model plausibly cover your target class, or is custom training unavoidable for your specific project?
4. If you expect class imbalance in a multi-class scheme, how will you address it, and how will you report per-class performance honestly?
5. What does your project actually need as a final deliverable: a raster mask, or a vectorized, GIS-ready output, and does your post-processing plan match that?
