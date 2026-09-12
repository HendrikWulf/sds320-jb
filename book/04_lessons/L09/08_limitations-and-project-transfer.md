---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Understanding the limits of image translation and applying it to your project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

You have now run the full LDSR-S2 workflow, from a single patch through tiled inference, uncertainty estimation, and basemap comparison. This page consolidates the limitations that have been building throughout the lesson into one clear picture, then turns to the same project-transfer work you completed at the end of earlier lessons: deciding, concretely, whether image translation belongs in your own project.

---

## 2. Which outputs are safe to use?

Super-resolution enhances visual detail; it does not recover true ground information. Every fine-scale feature in a super-resolved image is a statistical prediction, plausible given the low-resolution input, but not guaranteed to correspond to what actually exists on the ground.

This shows up in several concrete ways. A hallucination, in this generative-model sense, is a plausible-looking detail the model produced that does not correspond to reality: a building footprint that appears sharper than its true outline, a road edge generated where none exists, a vegetation texture that looks realistic but reflects a learned pattern rather than the actual canopy at that location. None of these are software bugs; they are the expected behavior of a model trained to generate convincing detail from ambiguous input, the same ill-posed-problem structure introduced several pages ago.

This distinction matters most for quantitative use. Measuring building areas, counting trees, or delineating field boundaries directly from super-resolved output can introduce systematic errors, since the model may fabricate, shift, or smooth features in ways that bias the resulting measurements. Uncertainty maps flag where the model is least confident, but as page 6 emphasized, low uncertainty does not guarantee correctness; a model can be confidently wrong.

Two further limitations are worth carrying into any project: super-resolution models inherit the biases of their training data, so performance can vary across landscape types the training data did not represent well, and spectral fidelity is not guaranteed, meaning output pixel values may not preserve the radiometric relationships needed for tasks like vegetation index computation or change detection, both of which depend on calibrated reflectance values.

```{admonition} A practical rule of thumb
:class: important
Treat super-resolved imagery as a visual enhancement and communication tool: useful for preliminary surveys, visual interpretation, and identifying areas that might warrant genuine high-resolution acquisition. For anything that depends on precise measurements, calibrated spectral values, or reliable feature geometry, use directly observed high-resolution imagery whenever it is available.
```

---

## 3. Project checklist

Work through this list for your own project:

- [ ] I can state specifically what my project would use super-resolved imagery for: visual interpretation, or quantitative measurement.
- [ ] If quantitative, I have identified an alternative (genuinely observed high-resolution imagery, or a different method) for anything I should not measure from generated detail.
- [ ] I have a plan to compute and check uncertainty maps for any super-resolution output I rely on.
- [ ] I know whether my project needs calibrated spectral values, and if so, I have confirmed super-resolved output is not the source for them.
- [ ] I have considered whether my study area resembles the kind of landscape the model was likely trained on.
- [ ] I have a plan for documenting, explicitly, which parts of any result depend on generated rather than observed detail.

---

## 4. Decision points

**Visual tool, or measurement source?** This is the central decision. If your project need is fundamentally visual (communication, preliminary survey, identifying areas of interest), super-resolution is a reasonable fit. If your project needs defensible measurements, prefer genuinely observed high-resolution imagery, and treat super-resolution as, at most, a way to decide where such imagery would be worth acquiring.

**How will you handle uncertainty?** Decide upfront whether you will compute and report uncertainty maps alongside any super-resolved output you use, and how you will treat high-uncertainty regions in your analysis or write-up.

**What will you explicitly not claim?** Given the hallucination risk, decide in advance which specific claims (exact building counts, precise boundary measurements, calibrated spectral analysis) you will not make from super-resolved output alone, and document that boundary clearly for anyone reading your project.

---

## 5. Common pitfalls

- **Using super-resolved output for a measurement without flagging it as generated.** This is the single most important documentation habit from this entire lesson; readers of your project need to know which numbers come from observed versus generated imagery.
- **Treating a visually convincing result as sufficient validation.** As emphasized throughout this lesson, realistic appearance is not evidence of accuracy.
- **Assuming model performance transfers uniformly across landscape types.** If your study area differs substantially from typical training data (unusual terrain, non-Western urban patterns, extreme climates), expect more caution to be warranted, not less.
- **Using super-resolved imagery for spectral index or change-detection work without checking radiometric fidelity first.** These tasks specifically depend on calibrated values that super-resolution does not guarantee.

---

## 6. Mini deliverable

Produce a short image-translation project plan (half a page is enough) that states:

1. Whether your project would use image translation (most likely super-resolution) at all, and specifically why.
2. Whether the intended use is visual or quantitative, and if quantitative, what alternative you will use for anything super-resolution should not measure.
3. Your plan for computing and interpreting uncertainty for any output you rely on.
4. One specific claim you will explicitly avoid making from generated imagery alone.
5. How you will document, in your project write-up, which results depend on generated versus observed imagery.

---

## 7. Reflection questions

1. Does your project's need for finer spatial detail call for a visual enhancement tool, or does it actually require genuinely observed high-resolution imagery?
2. If you used super-resolved imagery and later found it disagreed with ground truth in a specific area, how would you explain that disagreement using the concepts from this lesson?
3. How would you communicate the difference between "observed" and "generated" detail to someone reading your project who is not familiar with how these models work?
4. Would your study area's landscape type give you more or less confidence in a model's output, and why?
5. If your project does not currently need image translation, what would need to change about your data or your question for that to become useful?
