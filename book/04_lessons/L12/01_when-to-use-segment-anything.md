---
site:
  outline_maxdepth: 2
---

# When to use SAM

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Recognising when a general-purpose, prompt-based segmenter is the right tool
</div>
<!-- markdownlint-enable MD033 -->

---

The landing page introduced Segment Anything as a foundation-model shortcut around task-specific training. This page works through where that shortcut genuinely pays off, and where the earlier lessons' trained-model approach remains the better investment.

---

## 1. Motivation

A zero-shot result in minutes is appealing, but appealing is not the same as appropriate for every task. Knowing where {term}`SAM <Segment Anything Model>` tends to succeed, and where it tends to struggle, is what lets you use it efficiently rather than discovering its limits only after committing a project to it.

---

## 2. Core idea

{term}`Foundation model <Foundation Model>` segmentation fits well when your target feature is visually distinctive, recognizable from its shape, edges, or texture alone, and when an initial, unvalidated result is genuinely useful, for exploration, rapid label creation, or a first-pass inventory. It fits less well when your target depends on subtle, domain-specific distinctions the model was never trained to recognize, or when your project needs calibrated, validated accuracy rather than a plausible-looking boundary.

---

## 3. Where SAM tends to work well

### A. Exploration and label creation

If you need an initial segmentation to explore a study area, or a starting point for labels you will refine rather than final ground truth, SAM's speed is a genuine advantage: encode an image once, then try prompts cheaply, covered in more depth on the next page.

### B. Visually distinctive features

Building footprints, water bodies, agricultural fields, roads, and tree crowns share a property that makes them good SAM targets: a person could point at one in the image and describe it in a sentence, which is exactly the kind of visual distinctiveness a general-purpose model trained on diverse imagery tends to handle well.

### C. Choosing the right model

If your target depends on subtle spectral or radiometric distinctions rather than shape (distinguishing crop health stages, or specific land-cover types with similar visual appearance), a model trained specifically on your spectral bands and classes, as in L07, is likely to outperform a general-purpose segmenter working mostly from visual appearance. Similarly, if your project needs validated, quantifiable accuracy, an evaluation you can report with confidence, rather than a plausible-looking mask, a trained and properly evaluated model gives you that evaluation as part of the workflow; a zero-shot result still needs a separate validation step before you can make the same claim.

```{admonition} A quick mental test
:class: tip
Ask: "Could I point at this feature in the image and describe it in a sentence, and would a plausible-looking boundary already be useful to me?" If yes, SAM is a strong starting point. If your target needs subtle spectral distinctions or validated numeric accuracy, plan for a trained model, or at least a real validation step, on top of SAM's output.
```

---

## 4. Python reactivation

No new Python here. This page is a decision framework; the workflow itself begins two pages from now, once you understand SAM's internals conceptually on the next page.

---

## 5. Common pitfalls

- **Treating a zero-shot result as production-ready without validation.** A plausible-looking mask is a starting point, not proof of accuracy; the project-transfer page returns to this directly.
- **Expecting SAM to recognize domain-specific class names it was never trained to associate with text.** Text prompts work well for common, visually generic categories; highly specific domain vocabulary may not match anything the model learned to associate with an image region.
- **Using SAM for a task that actually needs calibrated measurement, not boundaries.** If your project needs biomass density or a calibrated index value, a boundary around a plausible region does not give you that; regression (L11) or a trained, evaluated model is the more appropriate tool.
- **Assuming SAM works equally well across all imagery types.** It generalizes broadly, but performance is not guaranteed to be uniform across every sensor, resolution, or geography; the same visual-inspection habits from earlier lessons still apply.

---

## 6. Mini task

Your project needs (a) a rough inventory of solar panel locations across a region for a feasibility study, and (b) validated, quantitative soil moisture estimates for an irrigation model. Which task fits a SAM-based workflow well, and which needs a trained, task-specific approach? Justify both.

:::{dropdown} Sample solution
:class: note

The solar panel inventory fits SAM well: panels are visually distinctive, an approximate, exploratory inventory is genuinely useful even before further validation, and the cost of a missed or extra detection is comparatively low at the feasibility stage. Soil moisture estimation needs a trained, task-specific approach (regression, from L11): it depends on calibrated, quantitative accuracy rather than a visually plausible boundary, and SAM has no mechanism for producing a validated numeric estimate of a physical quantity like moisture content.
:::

---

## 7. Key takeaways

- SAM-based workflows fit visually distinctive features where an initial, unvalidated result is genuinely useful.
- Subtle spectral distinctions and calibrated, quantitative accuracy needs still favor a trained, task-specific model.
- A zero-shot result is a starting point, not a substitute for validation, a theme this lesson returns to on later pages.
- The next page explains how SAM actually produces its segmentations, which will make the prompting workflow starting on page 4 easier to reason about.
