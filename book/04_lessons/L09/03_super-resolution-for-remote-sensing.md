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

You now have the conceptual vocabulary for image translation in general. This page makes the case for why {term}`super-resolution <Super-Resolution>` specifically matters for remote sensing, shows what it looks like next to simple enlargement, and surveys the main method families, before the next page explains how the model this lesson uses actually works.

---

## 1. Motivation

Every project that uses satellite imagery runs into the same underlying constraint eventually: better spatial detail, more frequent coverage, richer spectral information, and lower cost are all in tension with each other. Understanding this trade-off concretely is what makes super-resolution's actual value, and its actual limits, easier to judge for your own project.

---

## 2. Core idea

No single satellite sensor maximizes spatial resolution, revisit frequency, spectral coverage, and affordability simultaneously; every mission trades some of these against the others. {term}`Sentinel-2 <Sentinel-2>` prioritizes global coverage, frequent revisits, and rich spectral information, at the cost of spatial detail. Commercial sensors prioritize spatial detail, at the cost of coverage, revisit frequency, and price. Super-resolution is an attempt to partially move along this trade-off using computation instead of a different satellite.

---

## 3. The trade-off in practice

### A. Free and frequent, but coarse

Sentinel-2 covers the globe every five days or more often, with 13 spectral bands, entirely free to access, but its finest spatial resolution is 10 meters per pixel. At that resolution, individual buildings, narrow roads, and small agricultural plots are difficult or impossible to make out reliably.

### B. Detailed, but expensive or infrequent

The table compares typical sources. Resolutions are the finest multispectral (and, where available, panchromatic) pixel sizes:

| Source | Pixel size | Revisit | Access |
| --- | --- | --- | --- |
| Landsat 8/9 | 30 m (15 m panchromatic) | 8 days with both satellites | Free |
| **Sentinel-2** | **10 m** (RGB and near-infrared) | 5 days or better | Free |
| PlanetScope | about 3 m | Near-daily | Commercial (research and education programmes exist) |
| Pléiades Neo, WorldView-3 | about 1.2 m (0.3 m panchromatic) | On request, up to daily | Commercial |
| National aerial orthophotos (e.g. Spain's PNOA) | 0.25 m or finer | Every few years | Often free in Europe |

Commercial sensors such as WorldView-3 achieve sub-meter resolution, fine enough to resolve individual structures clearly, but they cover far less area per acquisition, image a given place only when tasked, and cost significantly more. Aerial orthophotos are very detailed and sometimes free, but they are only flown every few years, so they rarely match the date you need. Page 7 uses a Spanish aerial orthophoto as a reference, and its date turns out to matter.

### C. What super-resolution can(not) offer

Super-resolution attempts to close part of this gap by enhancing the spatial detail of freely available imagery through computation, rather than through a different acquisition. If reliable, this unlocks more detailed visual analysis, at global scale, without the cost of commercial imagery. It does not, however, add revisit frequency, spectral bands, or genuine new physical measurements; it only reconstructs plausible additional spatial detail from what is already present in the input.

That is a genuinely difficult problem, because it is **ill-posed**: many different high-resolution images produce exactly the same coarse image once averaged down to 10 m. A 10 m pixel that is half red roof and half grey street looks the same whether the roof is on the left or the right. This ambiguity is why simple **{term}`interpolation <Bicubic Interpolation>`**, which only smooths between existing pixel values, looks blurry rather than more detailed, and why super-resolution needs a model that has learned what fine-scale structures typically look like.

:::{figure} images/resolution_comparison.png
:alt: Three panels of the same city block in Granada, Spain. Left, Sentinel-2 at 10 m with visible square pixels. Centre, the same image enlarged four times with bicubic interpolation, smooth but blurry. Right, the LDSR-S2 super-resolved image at 2.5 m, with sharp building edges and street outlines.
:width: 100%
:align: center

*The same 640 × 640 m area of Granada at 10 m (left), enlarged to 2.5 m with bicubic interpolation (centre), and super-resolved to 2.5 m with LDSR-S2 (right). Interpolation adds pixels but no new structure; super-resolution adds structure the model considers plausible. Sentinel-2 data from page 5.*
:::

### D. Families of super-resolution methods

| Family | How it works | Sharpness | Risk of invented detail |
| --- | --- | --- | --- |
| **Interpolation** (nearest, bilinear, bicubic) | Smooth mathematical fill between existing pixels | Low | None: adds no structure |
| **{term}`Pansharpening <Pansharpening>`** | Injects detail from a sharper panchromatic band recorded at the same time | High | Low: the detail was observed (not available for Sentinel-2) |
| **Multi-image super-resolution** | Combines several slightly shifted acquisitions of the same place | Moderate | Low to moderate |
| **CNN with pixel loss** (e.g. SRCNN, EDSR) | Learns a mapping trained to minimise average error | Moderate | Low to moderate: tends to blur when unsure |
| **GAN** (e.g. SRGAN, ESRGAN) | Generator trained to fool a discriminator | High | High |
| **Diffusion** (e.g. SR3, LDSR-S2) | Iteratively generates detail consistent with the input | High | High, but can be *measured* by sampling several times (page 6) |

This is the perception–distortion trade-off from the previous page in practice: the further down the table, the sharper the output, and the more of it is generated rather than observed.

```{tip}
Keep this framing in mind for the rest of the lesson: super-resolution changes what an image looks like, not what the sensor actually measured. The next few pages explain how it does this, and the later uncertainty and limitations pages return directly to what that distinction means for using the output responsibly.
```

---

## 4. Common pitfalls

- **Assuming super-resolution eliminates the need for high-resolution imagery.** It narrows the gap for visual purposes; it does not replace genuinely observed high-resolution data for measurement-critical work, a point the limitations page develops further.
- **Assuming super-resolution improves spectral accuracy.** The trade-off it addresses is spatial resolution specifically; it adds no spectral bands and no new radiometric measurements.
- **Underestimating how ill-posed the underlying problem is.** Many plausible high-resolution images could produce the same low-resolution input; the model's job is to pick one plausible answer, not to recover the one true answer.
- **Comparing super-resolution only against the original 10 m image.** The fair comparison is against simple interpolation at the same pixel size; otherwise every method looks impressive.

---

## 5. Mini task

Your project needs to identify individual greenhouse structures across a large agricultural region, using only freely available imagery. Using the trade-off framework and tables from this page, explain why super-resolution might be worth trying here, which observed source you would check first, and what you would still want to verify before trusting the result.

:::{dropdown} Sample solution
:class: note

Super-resolution is worth trying because greenhouses are exactly the kind of small, discrete structure that 10-meter Sentinel-2 imagery struggles to resolve, and a free, computational enhancement could make more of them visually identifiable across a large region without commercial imagery costs.

Before generating anything, check whether free national aerial orthophotos cover the region (in Spain, PNOA at 0.25 m); if their date is close enough to your target date, they show the greenhouses directly. If you use super-resolution, verify enhanced structures against at least a sample of known greenhouse locations or high-resolution reference imagery, since the model is reconstructing plausible detail rather than directly observing individual structures, a distinction revisited on the uncertainty and limitations pages later in this lesson.
:::

---

## 6. Key takeaways

- Spatial resolution, revisit frequency, spectral coverage, and cost trade off against each other across satellite missions; no sensor maximizes all four.
- Sentinel-2 prioritizes coverage, frequency, and spectral richness over spatial detail; commercial sensors and aerial orthophotos offer detail at higher cost or lower frequency.
- Super-resolution narrows the spatial-detail gap computationally, without adding new physical measurements, revisit frequency, or spectral information.
- Single-image super-resolution is ill-posed: interpolation adds pixels but no structure, while learned models add structure that is plausible, not observed.
- Method families range from interpolation (no invented detail) to GANs and diffusion (sharp, partly invented detail).

### Further reading

- Esri, ["Super-Resolution: Image-to-Image Translation Using Deep Learning in ArcGIS Pro"](https://www.esri.com/arcgis-blog/products/arcgis-pro/aec/super-resolution-image-to-image-translation-using-deep-learning-in-arcgis-pro) — a different platform's perspective on the same underlying trade-off and technique.
- Saharia et al. (2021), ["Image Super-Resolution via Iterative Refinement"](https://arxiv.org/abs/2104.07636) — SR3, the first diffusion model for super-resolution.
- ESA, ["Sentinel-2 User Guide"](https://sentiwiki.copernicus.eu/web/s2-mission) — bands, resolutions and revisit of Sentinel-2.
- IGN Spain, ["PNOA – Plan Nacional de Ortofotografía Aérea"](https://pnoa.ign.es/) — the Spanish national orthophoto programme used as a reference on page 7.
