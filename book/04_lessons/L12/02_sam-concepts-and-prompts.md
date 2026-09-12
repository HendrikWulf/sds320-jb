---
site:
  outline_maxdepth: 2
---

# SAM concepts

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
How SAM turns a prompt into a segmentation mask
</div>
<!-- markdownlint-enable MD033 -->

---

You now know where SAM fits. This page explains, at a conceptual level, how it actually works, which will make the prompting workflow starting two pages from now feel like a natural consequence of the architecture, rather than a set of function calls to memorize.

---

## 1. Motivation

Every `samgeo` workflow in this lesson calls `set_image()` once and then tries multiple prompts against it. Understanding *why* that pattern exists, rather than just following it, is what will let you reason about cost and design efficient workflows for your own imagery.

---

## 2. Core idea

SAM 3's architecture has three core components for image segmentation, plus a fourth for video: an **image encoder** that processes the input once into a rich feature representation, a **prompt encoder** that converts your hints into a compatible format, and a **{term}`mask decoder <Mask Decoder>`** that combines the two into segmentation masks. The image encoder is expensive to run and only needs to run once per image; every {term}`prompt <Prompt>` after that is cheap, which is the architectural reason the "encode once, prompt many times" workflow works at all.

---

## 3. The four components

### A. Image encoder

The image encoder is a {term}`Vision Transformer (ViT) <Vision Transformer>` that processes the input image into an {term}`image embedding <Image Embedding>`, a high-dimensional feature representation capturing shapes, textures, edges, and spatial relationships. This is the single most computationally expensive step in the whole pipeline, but because SAM was trained on an enormous and diverse dataset, this embedding generalizes well even to imagery the model has never specifically encountered, including satellite and aerial photos. Crucially, it only runs once per image; `samgeo`'s `set_image()` call is what triggers this step and caches the result.

### B. Prompt encoder

The prompt encoder converts your hint, whichever form it takes, into a format the model can combine with the cached image embedding. A **prompt** can be a text description, one or more point coordinates (each labeled foreground or background), or a bounding box; the next two pages work through all three in practice. The prompt encoder is intentionally lightweight, which is what makes trying a new prompt fast compared to the one-time cost of encoding the image itself.

### C. Mask decoder

The mask decoder takes the cached image embedding and the encoded prompt, and produces the final segmentation output. For each prompt, it returns candidate masks along with {term}`confidence scores <Confidence Score>`, letting the model express uncertainty when a prompt is genuinely ambiguous (a point placed near an edge, for instance, might plausibly belong to either of two adjacent objects). These confidence scores are what you will later save alongside masks for downstream quality filtering, not a single global "how good is this image" number, but a per-prediction estimate.

### D. Streaming memory for video

For video, SAM 3 adds a **streaming memory** mechanism: as the model processes each new frame, it maintains a memory bank of previously seen frames and their segmentation results. This memory bank informs segmentation of objects that have moved, become partly occluded, or reappeared, which is what lets SAM track an object coherently across hundreds of frames without treating each frame as an independent, from-scratch segmentation problem. You will use this directly on the video page later in this lesson.

```{admonition} Why "encode once, prompt cheaply" matters for your workflow
:class: note
Because the expensive step happens only once per image, an efficient `samgeo` workflow calls `set_image()` a single time and then experiments freely with different prompts against the cached embedding. Re-loading the image or re-initializing the model for every new prompt wastes the exact efficiency this architecture was designed to provide.
```

---

## 4. Python reactivation

No new Python syntax here. This page's concepts map directly onto the method names you will use starting two pages from now: `set_image()` corresponds to the image encoder step, and the various `generate_masks*()` methods correspond to the prompt encoder and mask decoder working together on a specific prompt.

---

## 5. Common pitfalls

- **Assuming each new prompt re-runs the expensive image encoding step.** It does not; that is precisely the point of caching the image embedding, and re-initializing the model unnecessarily wastes this advantage.
- **Treating confidence scores as a single quality measure for the whole image.** They are per-prediction, tied to a specific mask from a specific prompt, not a global accuracy statistic.
- **Forgetting that ambiguous prompts genuinely produce multiple candidate masks.** This is expected behavior from the mask decoder, not an error, and is exactly what lets you refine a segmentation with additional prompts, covered on the following pages.
- **Expecting streaming memory to work identically to per-frame segmentation.** It specifically maintains object identity across frames, which is a different (and for tracking purposes, more useful) guarantee than segmenting each frame independently would give you.

---

## 6. Mini task

A classmate says, "SAM must re-analyze the whole image every time you give it a new prompt, since it produces a new mask each time." Using the ideas from this page, explain what this description gets wrong.

:::{dropdown} Sample solution
:class: note

It conflates two different steps. The image encoder, the expensive step that "analyzes the whole image," runs only once, when `set_image()` is called. Each new prompt afterward only involves the lightweight prompt encoder and mask decoder working against that already-cached image embedding, which is specifically why trying many prompts is fast; the model is not repeating the expensive analysis for every prompt, only the cheap comparison step.
:::

---

## 7. Key takeaways

- SAM's architecture separates an expensive, once-per-image encoding step from cheap, per-prompt mask generation.
- Prompts can be text, points (foreground or background), or boxes, all converted by the prompt encoder into a format the mask decoder can use.
- The mask decoder returns candidate masks with confidence scores, allowing the model to express uncertainty on ambiguous prompts.
- Streaming memory extends this architecture to video, maintaining object identity across frames rather than segmenting each frame independently.
