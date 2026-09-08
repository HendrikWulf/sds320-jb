---
site:
  outline_maxdepth: 2
---

# Change types

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Types of change and the challenges of comparing imagery across time
</div>
<!-- markdownlint-enable MD033 -->

---

Before running any method, it helps to be precise about what kind of change you are looking for, and to check that your two images are actually comparable in the first place. This page covers both, and the checklist here applies whether you end up using the traditional methods on the next page or the deep learning workflow later in this lesson.

---

## 1. Motivation

A change detection workflow built without considering these questions tends to produce results that are technically correct but practically useless, flagging seasonal leaf-drop as deforestation, or missing a real change because two images were never properly aligned. A few minutes of thinking here saves much more time later.

---

## 2. Core idea

Not all changes behave the same way, and the nature of the change you are looking for shapes every later decision, including how far apart your comparison dates should be.

- **Abrupt** changes (demolition, fire, flood) happen suddenly and are typically the easiest to detect, since they produce large spectral differences even between closely spaced dates.
- **Gradual** changes (urban sprawl, coastal erosion, glacier retreat) unfold over months or years; the difference between any two consecutive images may be too subtle to distinguish from noise, which is why detecting gradual change often needs a longer time series (**multitemporal** analysis) rather than a single before/after comparison (**{term}`bitemporal analysis <Bitemporal Analysis>`**).
- **Seasonal** changes are cyclical and natural (leaves, crop cycles, snow); a workflow that cannot distinguish these from genuine change will flag them constantly and uselessly.
- **Permanent versus temporary** matters for date selection: detecting new construction calls for image pairs separated by months or years, while flood monitoring might compare images just days apart, since floodwaters can recede quickly.

---

## 3. A comparability checklist

### A. Co-registration

If two images are not precisely aligned pixel to pixel, **{term}`co-registration <Co-registration>`** failure alone will produce false changes, sometimes even from sub-pixel offsets, especially along high-contrast edges like roads and building corners. Accurate co-registration is a prerequisite for any of the methods in this lesson, not an optional refinement.

### B. Atmospheric and illumination effects

Haze, cloud shadows, sun elevation angle, and view geometry all alter pixel values without any real surface change. Atmospheric correction (converting raw sensor values to surface reflectance) and relative radiometric normalization between dates both help reduce these effects before you look for genuine change.

### C. Seasonal and phenological variation

**{term}`Phenology <Phenology>`**, the study of cyclical, seasonal changes in vegetation, explains why a forest looks markedly different in summer versus winter, entirely apart from any land-cover change. Comparing images from similar seasons, or using spectral indices less sensitive to phenological state, helps keep this variation from swamping genuine change signals.

### D. Mixed pixels and spatial resolution

A **{term}`mixed pixel <Mixed Pixel>`** contains more than one land-cover type within its footprint, for example a 30-meter Landsat pixel covering part rooftop, part lawn, part pavement. If a single small structure appears within that pixel, the resulting spectral change may be too small to detect at all. Higher-resolution imagery resolves finer changes, but at the cost of larger data volumes and greater sensitivity to the co-registration problem from step A.

```{admonition} Ask this before running any method
:class: tip
"Are these two images actually comparable?" is not a rhetorical question. Check co-registration, note the season and any obvious atmospheric differences, and consider whether your target change would even be visible at your imagery's resolution, before interpreting any difference as real.
```

---

## 4. Python reactivation

No new Python here. This page is diagnostic, checks to run mentally, or with quick visual inspection, before writing any change detection code, which starts on the next page.

---

## 5. Common pitfalls

- **Comparing images from different seasons without accounting for phenology.** This is one of the most common sources of false "change" in practice.
- **Assuming downloaded imagery is already co-registered.** Verify alignment rather than assuming it, especially when combining data from different sources or processing pipelines.
- **Choosing a bitemporal comparison for a gradual change process.** A single before/after pair may not reliably separate genuine gradual change from noise; consider whether a longer time series is actually needed.
- **Ignoring resolution limits for small-scale changes.** If your target change is smaller than a mixed pixel's footprint at your imagery's resolution, no method will reliably detect it without higher-resolution data.

---

## 6. Mini task

You want to detect new residential construction in a suburb using two Landsat scenes (30-meter resolution) taken exactly one year apart, one in summer and one in winter. List two specific comparability concerns from this page that apply here, and how you would address each.

:::{dropdown} Sample solution
:class: note

First, the summer-versus-winter mismatch introduces phenological variation (deciduous trees with versus without leaves, different crop states) that could be mistaken for change; using an index less sensitive to phenology, or acquiring both images from the same season in different years, would address this. Second, at 30-meter resolution, individual houses are smaller than a single pixel and likely to produce mixed-pixel effects, meaning a single new house might not be reliably detectable at all; if that limits the value of the analysis, higher-resolution imagery (NAIP, for example) would be a better source for object-scale changes like individual residential construction.
:::

---

## 7. Key takeaways

- Change type (abrupt, gradual, seasonal, permanent or temporary) shapes how far apart your comparison dates should be, and whether bitemporal or multitemporal analysis fits better.
- Co-registration errors alone can produce false changes, especially at high-contrast edges; verify alignment before trusting any difference.
- Atmospheric, illumination, and phenological variation all alter pixel values without real surface change, and need to be accounted for or minimized.
- Mixed pixels limit what a given spatial resolution can reliably detect; check this before expecting a method to find small-scale change.
