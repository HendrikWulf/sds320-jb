# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning this lesson into concrete progress on your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

Every workflow page so far has run inference on one test image at a time, useful for learning the pipeline, but not how a real project processes a study area. This page closes the technical side of the lesson with batch inference, then turns to the same project-transfer work you completed at the end of L06 and L07: deciding, concretely, whether instance segmentation fits your own project, and sketching the specific choices it would need.

---

## 2. Batch inference across many images

When you have more than a handful of test images, processing them one at a time means reloading the model repeatedly and underusing available GPU resources. {term}`Batch inference <Batch Inference>` applies the same trained model to every image in a directory in one call:

```{code-cell} python
geoai.instance_segmentation_batch(
    input_dir=data["test_dir"],
    output_dir="field_boundaries/predictions",
    model_path=model_path,
    num_classes=2,
    num_channels=4,
    window_size=256,
    overlap=128,
    confidence_threshold=0.5,
    batch_size=4,
)
```

Every parameter here matches what you already chose on the inference page; batch processing changes *how many images* the call covers, not *how* each one is processed. The output directory receives one prediction per input image, ready for the same cleaning, vectorization, and property-extraction steps from the previous page, applied across your whole study area rather than a single test chip.

```{tip}
For a real project, run the cleaning and vectorization steps from the previous page in a loop over the batch output, then merge the resulting GeoDataFrames into one combined dataset before computing summary statistics across your full study area.
```

---

## 3. Project checklist

Work through this list for your own project:

- [ ] I can name the specific object(s) my project needs to detect, separate, and measure individually.
- [ ] I have confirmed my target objects have meaningful, non-trivial shapes worth tracing precisely, not just rough locations.
- [ ] I have checked my target object size against my imagery's spatial resolution.
- [ ] I know whether my label source already encodes instance IDs, or whether I will need to create or derive them.
- [ ] I have a plan for input bands (`num_channels`) matching my available imagery.
- [ ] I have a confidence-threshold strategy appropriate to my use case.
- [ ] I have a post-processing plan: cleaning parameters, vectorization, and which geometric properties I actually need.

---

## 4. Decision points

**Instance segmentation, or a simpler task?** Revisit the decision framework from the first page of this lesson. Commit to instance segmentation only if your project genuinely needs both object separation and precise shape.

**Where will instance labels come from?** A benchmark dataset like FTW may already cover your target class; if not, realistically estimate how long instance-level annotation will take for your own data, since it is more expensive than either classification or detection labels.

**What post-processing parameters fit your objects?** `min_area` and `max_hole_area` in `clean_instance_mask()` should reflect your actual target object sizes; a threshold copied from this lesson's field-boundary example may not suit much smaller or larger objects.

**What does your project's deliverable actually need?** A raster instance mask may be enough for a quick visual assessment; a deliverable that needs to be counted, measured, or joined with other GIS data needs the full vectorization and geometric-property pipeline from the previous page.

---

## 5. Common pitfalls

- **Scoping a project around objects too small for available imagery resolution.** As with detection and segmentation, this is the most common way an instance segmentation project underperforms.
- **Underestimating instance-level labeling time.** If you need custom labels, budget realistically; this is the most expensive labeling task covered in the course so far.
- **Copying post-processing thresholds without adapting them.** `min_area`, `max_hole_area`, and any geometric-property filters should match your own objects' typical size and shape, not the field-boundary example's defaults.
- **Treating batch inference output as automatically clean.** Batch processing scales up inference, not quality control; the same visual spot-checks from earlier pages still apply, now across more images.

---

## 6. Mini deliverable

Produce a short instance segmentation project plan (half a page is enough) that states:

1. Your target object(s), and why both separation and shape matter for your project question.
2. Your label source (existing dataset, or your own annotation plan with a realistic time estimate).
3. Your planned input bands and any resolution considerations for your target object size.
4. Your confidence-threshold strategy and your post-processing plan (cleaning parameters, vectorization, geometric properties).
5. One likely failure mode you anticipate, based on what you now know from this lesson.

---

## 7. Reflection questions

1. Does your project genuinely need instance-level separation, or would detection (L06) or semantic segmentation (L07) already answer your question at lower cost?
2. If your target objects vary widely in size, how would you choose `min_area` and `max_hole_area` values that work across that range?
3. What geometric properties (area, elongation, solidity, or others) would your project actually use, and why?
4. If instance labels are not readily available for your target class, how would you realistically plan for creating them?
5. What would a reasonable confidence threshold look like for your project, and what trade-off does it reflect?
