# L09 - Image translation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Transforming one image representation into another, such as sharpening resolution
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

L06 through L08 all extract information from imagery: a class, a box, or a per-pixel mask. This lesson introduces a different kind of task. {term}`Image translation <Image Translation>` does not label anything; it transforms one image into another, changing a property like resolution, spectral content, or sensor type while keeping the underlying spatial structure, buildings, roads, and fields, in the same places.

This lesson focuses on one specific, well-documented application: {term}`super-resolution <Super-Resolution>`, enhancing Sentinel-2 imagery from 10-meter to 2.5-meter resolution using a diffusion-based model. Looking ahead, L10 covers change detection, which compares images across time; the visual-comparison and interpretation habits you build here carry forward directly.

---

## 2. Motivation

Satellite imagery involves a persistent trade-off between spatial resolution, revisit frequency, spectral coverage, and cost. Sentinel-2 covers the globe every few days with rich spectral information, but its finest resolution is 10 meters per pixel, too coarse to reliably identify individual buildings or narrow roads. Commercial imagery reaches sub-meter resolution but costs far more and covers far less area.

Super-resolution offers a way to partially bridge that gap: using a trained model to enhance freely available imagery, producing output that approaches commercial-grade detail without the commercial-grade cost. For a project without a budget for high-resolution acquisition, this can meaningfully expand what is visually assessable, provided you understand exactly what the enhanced output can and cannot be trusted for, which is the throughline of this entire lesson.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- Decide when image translation, and super-resolution specifically, is an appropriate tool for a project
- Explain paired and unpaired image translation at a conceptual level, and why translated outputs are generated rather than observed
- Describe how a latent diffusion model performs super-resolution through encoding, denoising, and decoding
- Run single-patch and tiled super-resolution on Sentinel-2 imagery with `geoai`, and verify the output's georeferencing
- Compute and interpret per-pixel uncertainty maps, and explain why low uncertainty does not guarantee correctness
- Identify what super-resolved imagery is, and is not, safe to use for in a project

---

## 4. Lesson roadmap

1. [When translation fits](L09/01_when-to-use-image-translation.md) — the range of image translation tasks, and why this lesson focuses specifically on super-resolution.
2. [Translation concepts](L09/02_image-to-image-translation-concepts.md) — domains, paired versus unpaired translation, and why outputs are generated images.
3. [Why super-resolution matters](L09/03_super-resolution-for-remote-sensing.md) — the resolution-revisit-cost trade-off in satellite imagery, and what super-resolution can offer.
4. [Latent diffusion and LDSR-S2](L09/04_latent-diffusion-and-ldsr-s2.md) — how diffusion models generate detail, and the specific LDSR-S2 workflow this lesson uses.
5. [Single-patch super-resolution](L09/05_single-patch-super-resolution.md) — the main hands-on workflow: downloading Sentinel-2 imagery and enhancing one patch.
6. [Uncertainty and interpretation](L09/06_uncertainty-and-interpretation.md) — computing per-pixel uncertainty maps and reading them responsibly.
7. [Tiled inference and comparison](L09/07_tiled-inference-and-visual-comparison.md) — scaling to larger regions and comparing output against basemap imagery.
8. [Limitations & project transfer](L09/08_limitations-and-project-transfer.md) — what not to trust super-resolved imagery for, and applying this lesson to your project.

Pages 1–2 build the conceptual foundation, pages 3–4 explain why and how super-resolution works, page 5 is the main hands-on workflow, and pages 6–7 extend it with uncertainty and scale. Page 8 closes with limitations and your own project.

---

## 5. Project framing

While reading this lesson, keep your own project in mind and consider:

- Would enhanced spatial detail actually change what your project can conclude, or only how it looks?
- Does your project need precise measurements (areas, counts, boundaries), or mainly visual interpretation and communication?
- What imagery do you currently have access to, and where does its resolution genuinely limit your analysis?
- If you used super-resolved imagery, how would you document which parts of your results depend on generated, rather than observed, detail?

---

## 6. Before class

- Review the L07 and L08 lessons on segmentation, since this lesson contrasts image translation against those label-producing tasks directly.
- Make sure `geoai` is installed and importable in your environment.
- Think about whether any part of your own project currently runs into a resolution limitation.

---

## 7. After this lesson

After working through this lesson, you should have:

- A clear decision on whether super-resolution, or image translation more broadly, has a legitimate role in your project
- A working understanding of the full LDSR-S2 workflow, from raw Sentinel-2 imagery to uncertainty-aware, georeferenced output
- A clear sense of what generated imagery is safe to use for, and what it is not, ready to document explicitly if you use it
