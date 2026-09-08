---
site:
  outline_maxdepth: 2
---

# Translation concepts

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Domains, paired data, and the difference between generated and observed.
</div>
<!-- markdownlint-enable MD033 -->

---

The previous page scoped what this lesson covers. This page builds the conceptual vocabulary, domains, paired versus unpaired training, and generated-versus-observed output, that the rest of the lesson relies on, even though only super-resolution gets a full workflow.

---

## 1. Motivation

The single most important idea in this entire lesson is introduced here: a translated image is a plausible reconstruction, not a direct observation. Understanding why that is true, structurally, from how these models are trained, is what will let you use super-resolved imagery responsibly on the later pages, rather than as a rule you simply have to take on faith.

---

## 2. Core idea

Image translation learns a mapping from an input image in one domain to an output image in a different domain. A "domain" here just means a particular kind of image: 10-meter Sentinel-2 imagery is one domain, 2.5-meter imagery is another; SAR imagery is one domain, optical imagery is another. Crucially, the mapping preserves spatial structure: a building in the input stays in the same location in the output, even though its appearance changes.

---

## 3. Building the vocabulary

### A. Paired and unpaired translation

Early, influential translation frameworks like Pix2Pix learn from **paired** data: aligned examples of the same scene in both domains (a low-resolution image and its exact high-resolution counterpart). CycleGAN later extended the idea to **unpaired** settings, where aligned pairs are not available, useful when you have examples of both domains but no way to match them one-to-one. Which setting applies depends entirely on what training data exists for a given translation task; it is a property of the training setup, not something you choose independently at inference time.

### B. How these models learn to translate

Pix2Pix, CycleGAN, and similar models are built on **{term}`generative adversarial networks (GANs) <Generative Adversarial Network (GAN)>`**: a generator network produces candidate output images, and a discriminator network tries to tell generated images apart from real ones. Training pushes the generator to produce increasingly convincing output, since it improves specifically by fooling the discriminator, not by minimizing pixel-by-pixel error alone. This is conceptual background here; the model you will actually use in this lesson takes a different, more recent approach, covered on the next page.

### C. Generated, not observed

Because a translation model is trained to produce *convincing* output rather than to guarantee a physically correct one, its output is fundamentally a statistical prediction: the most plausible image consistent with the input, given everything the model learned during training. This matters because the model has no way to verify its output against ground truth at the moment it generates it; it produces its best guess, and that guess can be wrong, confidently and consistently wrong, in ways that are not obvious just by looking at the result.

```{admonition} Keep this distinction in view for the rest of the lesson
:class: important
Every later page in this lesson, including the workflow itself, builds on this one idea: translated output is generated, not measured. The uncertainty and limitations pages later in this lesson exist specifically because of this distinction, not as an afterthought.
```

---

## 4. Python reactivation

No new Python here. This page is conceptual groundwork for reading model output critically on later pages, not for writing code.

---

## 5. Common pitfalls

- **Treating "paired" and "unpaired" as a choice you make.** It is determined by what training data was available when the model was built, not a setting you control when using a pre-trained model.
- **Assuming a convincing-looking output is a correct one.** GANs and other generative approaches are specifically trained to produce realistic-looking results, which is a different goal from producing measurably accurate ones.
- **Forgetting that "generated, not observed" applies to every translation task, not just this lesson's super-resolution example.** The same caution applies to sensor translation, gap filling, and the other applications from the previous page.

---

## 6. Mini task

A classmate says, "the super-resolved image looks so sharp and realistic, it must be accurate." Using the concepts from this page, explain what is wrong with this reasoning in two or three sentences.

:::{dropdown} Sample solution
:class: note

Looking realistic and being accurate are not the same thing, precisely because these models are trained to produce plausible, convincing output rather than to guarantee correctness against ground truth. A model can generate a sharp, consistent, visually convincing detail, a road edge, a building corner, that simply does not correspond to anything actually on the ground, since it is filling in detail statistically rather than observing it directly.
:::

---

## 7. Key takeaways

- Image translation maps an input image from one domain to an output image in another, preserving spatial structure while changing a specific property.
- Paired training data (Pix2Pix) uses aligned examples in both domains; unpaired training data (CycleGAN) does not require alignment.
- GANs train a generator to produce convincing output by competing against a discriminator, optimizing for realism rather than direct pixel accuracy.
- Translated output is a generated, plausible reconstruction, not a direct observation; this distinction underlies the uncertainty and limitations pages later in this lesson.
