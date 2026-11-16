---
site:
  outline_maxdepth: 2
---

# When translation fits

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A quick tour of the family, and why this lesson narrows in on one member of it
</div>
<!-- markdownlint-enable MD033 -->

---

The landing page introduced image translation as a task that produces new images rather than labels. This page surveys the range of things that idea covers, compares it with observed alternatives, and then narrows the focus to what the rest of this lesson actually teaches.

---

## 1. Motivation

"Image translation" covers a genuinely broad family of tasks, and it is easy to assume a lesson on it will cover all of them in depth. Knowing upfront what this lesson does and does not teach will help you get the most out of it, and point you toward further reading if your project needs one of the tasks covered only briefly here. Just as important: for several of these tasks, there is an *observed* alternative that does not involve generating any pixels at all, and you should know it before reaching for a generative model.

---

## 2. Core idea

{term}`Image translation <Image Translation>` covers several distinct applications in remote sensing. Each transforms one property of an image while preserving its underlying spatial layout:

| Task | Input → output | Remote sensing example | Main risk |
| --- | --- | --- | --- |
| **{term}`Super-resolution <Super-Resolution>`** | Coarse image → finer image of the same scene | {term}`Sentinel-2` at 10 m → 2.5 m (this lesson) | Invented fine detail: edges, roofs, small objects |
| **Sensor translation** | One modality → another | Radar (SAR) → optical-looking image | Plausible colours and textures that radar never measured |
| **Temporal gap filling** | Neighbouring dates → missing date | {term}`Cloud removal <Cloud Removal>` in a cloudy scene | Whole areas filled with plausible but unobserved content |
| **Spectral enhancement** | Few bands → more bands | RGB → estimated near-infrared | Reflectance values that look right but are not measured |
| **Synthetic data generation** | Labels or noise → realistic image | Training images for rare classes or regions | Training on artefacts the model then learns as real |

All five share the same structure: a model learns to map an input image from one domain to an output image in a different domain, changing resolution, spectral content or sensor style while keeping structural features like buildings, roads and fields in their correct locations. And all five share the same risk, shown in the last column: the output contains information that no sensor observed.

---

## 3. Points to consider

### A. Why focus on super-resolution

This lesson works through super-resolution in depth, downloading real Sentinel-2 imagery, running a trained model, and evaluating its output, because it is one of the most practically important and best-documented image translation applications for remote sensing, and because the interpretation habits it teaches (checking uncertainty, being explicit about what is generated versus observed) transfer directly to the other four applications above.

If your own project needs sensor translation, temporal gap filling, spectral enhancement, or synthetic data generation specifically, the concepts on the next page still apply, but you will need to look beyond this lesson for a worked example of those specific tasks.

### B. Observed alternatives come first

Before generating pixels, check whether the information you need can be *observed* instead. Each translation task has a non-generative counterpart:

| Need | Observed alternative | Translation option |
| --- | --- | --- |
| More spatial detail | **{term}`Pansharpening <Pansharpening>`** with a sharper panchromatic band the satellite really recorded (Landsat 8/9: 15 m panchromatic with 30 m multispectral bands); or buy very high-resolution imagery | Super-resolution |
| A cloud-free view on a given date | A cloud-free scene a few days earlier or later, a multi-date composite, or radar, which sees through clouds | Temporal gap filling, cloud removal |
| Information from another sensor type | Acquire that sensor's data for the same date | Sensor translation |
| More spectral bands | A sensor that records those bands | Spectral enhancement |

Sentinel-2 has no panchromatic band, so pansharpening is not an option for it, which is one reason why learned super-resolution of Sentinel-2 has attracted so much research. Even then, the question of whether you need generated detail at all stays relevant: page 3 shows what very high-resolution imagery costs, and page 8 returns to this decision for your own project.

```{admonition} A quick mental test
:class: tip
Ask: "Am I trying to see more spatial detail in imagery I already have?" If yes, super-resolution, this lesson's focus, is directly relevant. If your actual need is a different kind of transformation (a different sensor's appearance, a missing date, extra bands), the general concepts here still apply, but you are working with a different specific task. In both cases, ask next: "Could I observe this instead?"
```

---

## 4. Common pitfalls

- **Assuming any resolution problem calls for super-resolution.** If your project's actual bottleneck is spectral coverage or revisit frequency rather than spatial detail, a different translation task, or a different imagery source entirely, may be more appropriate.
- **Skipping the observed alternatives.** Pansharpening, a cloud-free neighbouring date or a radar acquisition gives you measured information; a generative model gives you a plausible guess.
- **Expecting this lesson to cover sensor translation, gap filling, or synthetic data generation in the same depth as super-resolution.** They are related in concept but not covered here as full workflows.
- **Treating "image translation" as a vague catch-all.** Each of the five applications above solves a specific, different problem; being precise about which one your project needs will help you find the right tool.

---

## 5. Mini task

Your project has good Sentinel-2 coverage of a study area, but a persistent cloud bank obscures the exact date you need for a seasonal comparison. Which of the five image translation applications above best matches this problem, and why is it not the same problem super-resolution solves? Name one observed alternative you would try first.

:::{dropdown} Sample solution
:class: note

This is a temporal gap filling problem: the imagery for the needed date is missing or unusable, not too coarse. Super-resolution assumes you already have a usable, if coarse, observation for the date you need, and enhances its spatial detail; it does nothing to help when no usable observation exists at all for that date. These are genuinely different problems even though both fall under the image translation umbrella.

Before generating a cloud-free image, check whether a cloud-free Sentinel-2 acquisition a few days earlier or later is close enough for the seasonal comparison (Sentinel-2 revisits every few days), build a composite of several dates, or use Sentinel-1 radar, which is not blocked by clouds.
:::

---

## 6. Key takeaways

- Image translation covers super-resolution, sensor translation, temporal gap filling, spectral enhancement, and synthetic data generation, all sharing the same domain-to-domain mapping structure.
- All five produce information that no sensor observed; that shared risk is the thread running through this lesson.
- Each task has an observed alternative, such as pansharpening, a neighbouring cloud-free date or another sensor, that is worth checking first.
- This lesson focuses specifically on super-resolution, using it as a deep, worked example rather than surveying all five applications equally.
- The next page builds the conceptual vocabulary (domains, paired versus unpaired translation) that applies across all five applications.

### Further reading

- Isola et al. (2017), ["Image-to-Image Translation with Conditional Adversarial Networks"](https://arxiv.org/abs/1611.07004) — the Pix2Pix paper; one of its examples translates between aerial photos and maps.
- ESA OpenSR, ["opensr-model"](https://github.com/ESAOpenSR/opensr-model) — the open-source project behind the super-resolution model used in this lesson.
- Esri, ["Super-Resolution: Image-to-Image Translation Using Deep Learning in ArcGIS Pro"](https://www.esri.com/arcgis-blog/products/arcgis-pro/aec/super-resolution-image-to-image-translation-using-deep-learning-in-arcgis-pro) — a different platform's perspective on the same family of tasks.
