---
site:
  outline_maxdepth: 2
---

# When Translation Fits

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A quick tour of the family, and why this lesson narrows in on one member of it.
</div>
<!-- markdownlint-enable MD033 -->

---

The landing page introduced image translation as a task that produces new images rather than labels. This page surveys the range of things that idea covers, then narrows the focus to what the rest of this lesson actually teaches.

---

## 1. Motivation

"Image translation" covers a genuinely broad family of tasks, and it is easy to assume a lesson on it will cover all of them in depth. Knowing upfront what this lesson does and does not teach will help you get the most out of it, and point you toward further reading if your project needs one of the tasks covered only briefly here.

---

## 2. Core idea

{term}`Image translation <Image Translation>` covers several distinct applications in remote sensing, each transforming one property of an image while preserving its underlying spatial layout:

- **Super-resolution**: enhancing spatial resolution, the focus of this entire lesson.
- **Sensor translation**: converting between imaging modalities, such as generating optical-looking imagery from synthetic aperture radar (SAR) data, making radar imagery easier to interpret visually.
- **Temporal gap filling**: synthesizing plausible imagery for dates without a usable observation, for example when cloud cover blocks a scene.
- **Spectral enhancement**: adding spectral information to imagery that lacks it, such as estimating multispectral content from RGB-only input.
- **Synthetic data generation**: producing realistic training imagery for regions or conditions where labeled data is scarce.

All five share the same underlying structure: a model learns to map an input image from one domain to an output image in a different domain, changing resolution, spectral content, or sensor style while keeping structural features like buildings, roads, and fields in their correct locations.

---

## 3. Why focus on super-resolution

This lesson works through super-resolution in depth, downloading real Sentinel-2 imagery, running a trained model, and evaluating its output, because it is one of the most practically important and best-documented image translation applications for remote sensing, and because the interpretation habits it teaches (checking uncertainty, being explicit about what is generated versus observed) transfer directly to the other four applications above.

If your own project needs sensor translation, temporal gap filling, spectral enhancement, or synthetic data generation specifically, the concepts on the next page still apply, but you will need to look beyond this lesson for a worked example of those specific tasks.

```{admonition} A quick mental test
:class: tip
Ask: "Am I trying to see more spatial detail in imagery I already have?" If yes, super-resolution, this lesson's focus, is directly relevant. If your actual need is a different kind of transformation (a different sensor's appearance, a missing date, extra bands), the general concepts here still apply, but you are working with a different specific task.
```

---

## 4. Python reactivation

No new Python here. This page is scoping, not implementation; the workflow itself begins in page 5.

---

## 5. Common pitfalls

- **Assuming any resolution problem calls for super-resolution.** If your project's actual bottleneck is spectral coverage or revisit frequency rather than spatial detail, a different translation task, or a different imagery source entirely, may be more appropriate.
- **Expecting this lesson to cover sensor translation, gap filling, or synthetic data generation in the same depth as super-resolution.** They are related in concept but not covered here as full workflows.
- **Treating "image translation" as a vague catch-all.** Each of the five applications above solves a specific, different problem; being precise about which one your project needs will help you find the right tool.

---

## 6. Mini task

Your project has good Sentinel-2 coverage of a study area, but a persistent cloud bank obscures the exact date you need for a seasonal comparison. Which of the five image translation applications above best matches this problem, and why is it not the same problem super-resolution solves?

:::{dropdown} Sample solution
:class: note

This is a temporal gap filling problem: the imagery for the needed date is missing or unusable, not too coarse. Super-resolution assumes you already have a usable, if coarse, observation for the date you need, and enhances its spatial detail; it does nothing to help when no usable observation exists at all for that date. These are genuinely different problems even though both fall under the image translation umbrella.
:::

---

## 7. Key takeaways

- Image translation covers super-resolution, sensor translation, temporal gap filling, spectral enhancement, and synthetic data generation, all sharing the same domain-to-domain mapping structure.
- This lesson focuses specifically on super-resolution, using it as a deep, worked example rather than surveying all five applications equally.
- Being precise about which specific translation problem your project has will point you toward the right tool, since these five tasks solve genuinely different problems.
- The next page builds the conceptual vocabulary (domains, paired versus unpaired translation) that applies across all five applications, even though the rest of this lesson only implements one.
