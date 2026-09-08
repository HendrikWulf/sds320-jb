---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Deciding whether Segment Anything fits your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

This lesson covered a complete zero-shot workflow: when it fits, how SAM works conceptually, geospatial setup, three prompt types, building extraction, scaling, and interactive and video segmentation. This page is not about learning anything new. It is about deciding, concretely, whether Segment Anything supports your SDS320 project, and sketching the specific choices, target feature, prompt type, and validation plan, your project would need.

---

## 2. Project checklist

Work through this list for your own project:

- [ ] I can name the specific feature my project needs to segment, and confirm it is visually distinctive enough for a general-purpose model to plausibly recognize.
- [ ] I have decided which prompt type, text, point, or box, fits my target best, and why.
- [ ] I have access to Hugging Face approved (or a realistic timeline to obtain it) before I need to run any code.
- [ ] I have a plan to save and use confidence scores for downstream quality filtering, not just the hard mask.
- [ ] I know whether my imagery needs batch processing (many images), tiling (one large image), or neither.
- [ ] I have a concrete plan for validating results, beyond a visual glance, before relying on them for a project conclusion.
- [ ] I have considered whether a trained, task-specific model from an earlier lesson would actually serve my project better.

---

## 3. Decision points

**Zero-shot, or trained model?** Revisit the framework from the first page of this lesson. If your target is visually distinctive and an unvalidated first pass is genuinely useful, SAM fits. If your target needs subtle spectral distinctions or calibrated numeric accuracy, plan for a trained model instead, or in addition.

**Which prompt type?** Match your choice to what you actually know about your target: a text description if it is a common, visually generic category and broad coverage is what you need; a box if you can identify one clear example and want similar objects found elsewhere; points if you need precise, single-object control, especially if you already have coordinates (like centroids) to prompt from.

**What post-processing does your deliverable need?** A raster mask may suffice for visual assessment; a GIS deliverable needs vectorization, and possibly regularization or smoothing depending on whether your target has natural right angles (buildings) or curves (water bodies, tree crowns).

**How will you validate results?** Decide on a concrete plan, spot-checking against known locations, comparing against a small set of manually verified examples, or checking confidence scores against actual accuracy on a sample, rather than treating a visually plausible result as sufficient on its own.

**Does your project need batch or tiled processing?** If you have many images, plan for batch segmentation; if you have one large raster, plan for tiled segmentation; know which situation you are actually in before writing code.

---

## 4. Common pitfalls

- **Treating a zero-shot result as validated without a real check.** This is the most important habit from this entire lesson; a plausible mask is a starting point, not proof.
- **Choosing a prompt type by default rather than by fit.** Text prompts are convenient but not always the most precise choice; match your prompt type to what you actually know about your target.
- **Underestimating the Hugging Face access timeline.** Request access well before you need to run any code, not the day you plan to start.
- **Skipping confidence-score filtering entirely.** Saving and using scores, rather than accepting every detection equally, is what lets you distinguish reliable results from uncertain ones downstream.
- **Choosing SAM for a task that genuinely needs a trained, evaluated model.** Revisit the decision framework from page 1 if your project's accuracy needs go beyond what a zero-shot result can support.

---

## 5. Mini deliverable

Produce a short Segment Anything project plan (half a page is enough) that states:

1. Your target feature and why it is a reasonable fit for zero-shot segmentation (or why it is not, and what you will use instead).
2. Your planned prompt type, with a one-sentence justification.
3. Your plan for confidence-score filtering and post-processing (vectorization, regularization, or smoothing).
4. Whether your project needs batch or tiled processing, and why.
5. Your validation plan: how you will check results before relying on them.

---

## 6. Reflection questions

1. Is your target feature visually distinctive enough for a general-purpose model, or does it depend on distinctions a foundation model was never trained to make?
2. Which prompt type would give you the most control with the least manual effort for your specific target?
3. How will you validate a zero-shot result before using it in a project conclusion, beyond a visual glance?
4. If your project scales to many images or one very large raster, which scaling approach fits, and what parameters would you start with?
5. Would your project genuinely benefit from Segment Anything, or would the time be better spent on a trained, task-specific model from an earlier lesson?
