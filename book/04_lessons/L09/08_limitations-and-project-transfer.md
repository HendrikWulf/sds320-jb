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

You have now run the full {term}`LDSR-S2` workflow, from a single patch through uncertainty estimation, {term}`tiled inference <Tiled Inference>` and a comparison with {term}`aerial imagery <Aerial Imagery>`. This page consolidates the limitations that have been building throughout the lesson into one clear picture, then turns to the same project-transfer work you completed at the end of earlier lessons: deciding, concretely, whether image translation belongs in your own project.

---

## 2. Which outputs are safe to use?

### A. Measured versus generated

The pages of this lesson add up to a precise answer. LDSR-S2 keeps every **10 m average** exactly as Sentinel-2 measured it (page 5): averaged back to 10 m, the output matches the input, and so does an index such as {term}`NDVI <Normalized Difference Vegetation Index>`. What the model generates is the **arrangement inside each 10 m pixel**: where a roof ends, where a courtyard is, which 2.5 m pixel is a tree. That arrangement changes from run to run (page 6), and compared with aerial imagery it is often plausible texture rather than an accurate copy (page 7).

A **{term}`hallucination <Hallucination>`**, in this generative-model sense, is a plausible-looking detail the model produced that does not correspond to reality: a building outline sharper than its true shape, a road edge generated where none exists, a vegetation texture that reflects a learned pattern rather than the actual canopy. None of these are software bugs; they are the expected behavior of a model trained to generate convincing detail from ambiguous input, the ill-posed problem from page 3. On page 7, the model's roundabout turned out to be real, while the courtyards and street trees of the blocks of flats were not reproduced.

| Use of super-resolved imagery | Safe? | Why |
| --- | --- | --- |
| Visual interpretation, maps for communication, finding areas of interest | Yes, if labelled as generated | Large structures (roads, blocks, fields) match the observed layout |
| Values or indices averaged to 10 m or coarser | Yes, but there is no gain | They equal the original Sentinel-2 values; use the original data instead |
| Shapes, areas and counts of objects smaller than about 20 m (houses, trees, small fields) | No | Their outlines are generated and differ between runs |
| Indices or classification at 2.5 m (e.g. NDVI of a single tree crown) | No | The distribution inside each 10 m pixel is generated, not measured |
| Change detection between two super-resolved dates | No | Each date's detail is generated independently, so detail differences appear as false change |
| Training labels or training images for another model | Only with great care | The other model learns the generated textures as if they were real |

The change detection row connects directly to [Lesson 10](../10_change-detection.md): two super-resolved images of the same unchanged place differ because each run draws different detail. A change detector cannot tell this generated difference from a real one. If you need change at fine scales, compare the original 10 m images, or use observed high-resolution imagery for both dates.

### B. Where the model learned

{term}`Super-resolution <Super-Resolution>` models inherit the characteristics of their training data. LDSR-S2 was trained on **SEN2NAIP**, Sentinel-2 images paired with {term}`NAIP` aerial images of the United States. Its idea of what a roof, a street or a field looks like at 2.5 m therefore comes from American landscapes. Granada's dense blocks of flats with inner courtyards, terracotta roofs and narrow streets differ from most of that training data, which is one plausible reason why the blocks of flats on page 7 look plausible but not accurate. For your own study area, ask how similar it is to the training data, and expect more invented detail the more it differs.

```{admonition} A practical rule of thumb
:class: important
Treat super-resolved imagery as a visual enhancement and communication tool: useful for preliminary surveys, visual interpretation, and identifying areas that might warrant genuine high-resolution acquisition. For anything that depends on precise measurements, sub-10 m spectral values, reliable feature geometry, or comparisons between dates, use the original data or directly observed high-resolution imagery.
```

---

## 3. Project checklist

Work through this list for your own project:

- [ ] I can state specifically what my project would use super-resolved imagery for: visual interpretation, or quantitative measurement.
- [ ] I have checked observed alternatives first: free aerial orthophotos, pansharpened imagery, or commercial imagery (page 1 and page 3).
- [ ] If quantitative, I have identified an alternative (genuinely observed high-resolution imagery, or a different method) for anything I should not measure from generated detail.
- [ ] I have a plan to compute and check {term}`uncertainty maps <Uncertainty Map>` for any super-resolution output I rely on.
- [ ] I compare the output against observed reference imagery for at least a sample of locations, and I know the reference's acquisition date.
- [ ] I do not use super-resolved imagery to detect change between dates.
- [ ] I have considered whether my study area resembles the landscapes the model was trained on.
- [ ] I have a plan for documenting, explicitly, which parts of any result depend on generated rather than observed detail.

---

## 4. Decision points

**Visual tool, or measurement source?** This is the central decision. If your project need is fundamentally visual (communication, preliminary survey, identifying areas of interest), super-resolution is a reasonable fit. If your project needs defensible measurements, prefer genuinely observed high-resolution imagery, and treat super-resolution as, at most, a way to decide where such imagery would be worth acquiring.

**Is there observed imagery for your area and date?** Many countries publish aerial orthophotos for free. If one exists close enough to your date, it shows real detail at 25 cm instead of plausible detail at 2.5 m.

**How will you handle uncertainty?** Decide upfront whether you will compute and report uncertainty maps alongside any super-resolved output you use, and how you will treat high-uncertainty regions in your analysis or write-up.

**What will you explicitly not claim?** Given the hallucination risk, decide in advance which specific claims (exact building counts, precise boundary measurements, change between dates, spectral analysis below 10 m) you will not make from super-resolved output alone, and document that boundary clearly for anyone reading your project.

---

## 5. Common pitfalls

- **Using super-resolved output for a measurement without flagging it as generated.** This is the single most important documentation habit from this entire lesson; readers of your project need to know which numbers come from observed versus generated imagery.
- **Treating a visually convincing result as sufficient validation.** As shown on page 7, realistic appearance is not evidence of accuracy.
- **Assuming model performance transfers uniformly across landscape types.** If your study area differs substantially from the training data (here, the United States), expect more invented detail, not less.
- **Comparing super-resolved images from two dates.** Generated detail differs between runs, so it differs between dates even where nothing changed.
- **Computing indices at 2.5 m and treating them as measured.** Only their 10 m averages are measured; at 10 m, use the original Sentinel-2 bands directly.

---

## 6. Mini deliverable

Produce a short image-translation project plan (half a page is enough) that states:

1. Whether your project would use image translation (most likely super-resolution) at all, and specifically why, including which observed alternative you checked.
2. Whether the intended use is visual or quantitative, and if quantitative, what alternative you will use for anything super-resolution should not measure.
3. Your plan for computing and interpreting uncertainty, and for comparing against observed reference imagery.
4. One specific claim you will explicitly avoid making from generated imagery alone.
5. How you will document, in your project write-up, which results depend on generated versus observed imagery.

---

## 7. Reflection questions

1. Does your project's need for finer spatial detail call for a visual enhancement tool, or does it actually require genuinely observed high-resolution imagery?
2. If you used super-resolved imagery and later found it disagreed with {term}`ground truth <Ground Truth>` in a specific area, how would you explain that disagreement using the concepts from this lesson (consistency at 10 m, generated detail inside each pixel, uncertainty, training data)?
3. How would you communicate the difference between "observed" and "generated" detail to someone reading your project who is not familiar with how these models work?
4. Would your study area's landscape type give you more or less confidence in a model trained on images of the United States, and why?
5. If your project does not currently need image translation, what would need to change about your data or your question for that to become useful?

### Further reading

- Aybar et al. (2024), ["SEN2NAIP: A large-scale dataset for Sentinel-2 Image Super-Resolution"](https://doi.org/10.1038/s41597-024-04214-y), *Scientific Data* 11, 1389 — the training dataset of LDSR-S2: Sentinel-2 images paired with US aerial (NAIP) imagery.
- Donike, Aybar, Gómez-Chova and Kalaitzis (2025), ["Trustworthy Super-Resolution of Multispectral Sentinel-2 Imagery With Latent Diffusion"](https://doi.org/10.1109/JSTARS.2025.3542220), IEEE JSTARS — the LDSR-S2 paper.
- Blau and Michaeli (2018), ["The Perception-Distortion Tradeoff"](https://arxiv.org/abs/1711.06077) — why sharp output and accurate output cannot both be maximised.
