---
site:
  outline_maxdepth: 2
---

# Super-resolution

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Generating higher-resolution imagery from lower-resolution remote sensing input
</div>
<!-- markdownlint-enable MD033 -->

---

You now have the conceptual vocabulary for image translation in general. This page makes the case for why {term}`super-resolution <Super-Resolution>` specifically matters for remote sensing, before the next page explains how the model this lesson uses actually works.

---

## 1. Motivation

Every project that uses satellite imagery runs into the same underlying constraint eventually: better spatial detail, more frequent coverage, richer spectral information, and lower cost are all in tension with each other. Understanding this trade-off concretely is what makes super-resolution's actual value, and its actual limits, easier to judge for your own project.

---

## 2. Core idea

No single satellite sensor maximizes spatial resolution, revisit frequency, spectral coverage, and affordability simultaneously; every mission trades some of these against the others. {term}`Sentinel-2 <Sentinel-2>` prioritizes global coverage, frequent revisits, and rich spectral information, at the cost of spatial detail. Commercial sensors prioritize spatial detail, at the cost of coverage, revisit frequency, and price. Super-resolution is an attempt to partially move along this trade-off using computation instead of a different satellite.

---

## 3. The trade-off in practice

### A. Free and frequent, but coarse

Sentinel-2 covers the globe roughly every five days with 13 spectral bands, entirely free to access, but its finest spatial resolution is 10 meters per pixel. At that resolution, individual buildings, narrow roads, and small agricultural plots are difficult or impossible to make out reliably.

### B. Detailed, but expensive and sparse

Commercial sensors such as WorldView-3 achieve sub-meter resolution, fine enough to resolve individual structures clearly, but they cover far less area per acquisition, revisit specific locations far less often, and cost significantly more to access.

### C. What super-resolution can(not) offer

Super-resolution attempts to close part of this gap by enhancing the spatial detail of freely available imagery through computation, rather than through a different acquisition. If reliable, this unlocks more detailed visual analysis, at global scale, without the cost of commercial imagery. It does not, however, add revisit frequency, spectral bands, or genuine new physical measurements; it only reconstructs plausible additional spatial detail from what is already present in the input, a genuinely difficult problem, since many different high-resolution images could plausibly correspond to the same coarse observation once downsampled. This ambiguity is exactly why simple interpolation (stretching pixels bigger) looks blurry rather than genuinely more detailed, and why the more capable generative approach on the next page is needed instead.

```{tip}
Keep this framing in mind for the rest of the lesson: super-resolution changes what an image looks like, not what the sensor actually measured. The next few pages explain how it does this, and the later uncertainty and limitations pages return directly to what that distinction means for using the output responsibly.
```

---

## 4. Python reactivation

No new Python here. The workflow itself begins on page 5, once the next page explains how the specific model used in this lesson works.

---

## 5. Common pitfalls

- **Assuming super-resolution eliminates the need for high-resolution imagery.** It narrows the gap for visual purposes; it does not replace genuinely observed high-resolution data for measurement-critical work, a point the limitations page develops further.
- **Assuming super-resolution improves spectral accuracy.** The trade-off it addresses is spatial resolution specifically; it says nothing about spectral coverage or radiometric accuracy.
- **Underestimating how ill-posed the underlying problem is.** Many plausible high-resolution images could produce the same low-resolution input; the model's job is to pick one plausible answer, not to recover the one true answer.

---

## 6. Mini task

Your project needs to identify individual greenhouse structures across a large agricultural region, using only freely available imagery. Using the trade-off framework from this page, explain why super-resolution might be worth trying here, and what you would still want to verify before trusting the result.

:::{dropdown} Sample solution
:class: note

Super-resolution is worth trying because greenhouses are exactly the kind of small, discrete structure that 10-meter Sentinel-2 imagery struggles to resolve, and a free, computational enhancement could make more of them visually identifiable across a large region without commercial imagery costs. Before trusting the result, you would want to verify enhanced structures against at least a sample of known greenhouse locations or high-resolution reference imagery, since the model is reconstructing plausible detail rather than directly observing individual structures, a distinction developed further in the concepts page and revisited on the uncertainty and limitations pages later in this lesson.
:::

---

## 7. Key takeaways

- Spatial resolution, revisit frequency, spectral coverage, and cost trade off against each other across satellite missions; no sensor maximizes all four.
- Sentinel-2 prioritizes coverage, frequency, and spectral richness over spatial detail; commercial sensors prioritize spatial detail at higher cost and lower coverage.
- Super-resolution narrows the spatial-detail gap computationally, without adding new physical measurements, revisit frequency, or spectral information.
- Single-image super-resolution is inherently ill-posed: many plausible high-resolution images could correspond to the same low-resolution input, which is why the next page's generative approach is needed rather than simple upsampling.

### Further reading

- Esri, ["Super-Resolution: Image-to-Image Translation Using Deep Learning in ArcGIS Pro"](https://www.esri.com/arcgis-blog/products/arcgis-pro/aec/super-resolution-image-to-image-translation-using-deep-learning-in-arcgis-pro) — a different platform's perspective on the same underlying trade-off and technique.
