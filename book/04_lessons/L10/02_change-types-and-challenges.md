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

Before running any method, it helps to be precise about what kind of change you are looking for, and to check that your two images are actually comparable in the first place. This page covers both. The checklist here applies whether you end up using the traditional methods on the next page or the deep learning workflow later in this lesson.

---

## 1. Motivation

A change detection workflow built without considering these questions tends to produce results that are technically correct but practically useless: flagging seasonal leaf-drop as deforestation, or reporting "change" along every road because two images were never properly aligned. A few minutes of thinking here saves much more time later.

---

## 2. Core idea

Two images of the same place differ for two kinds of reasons: because the surface **really changed**, and because the **observation changed** (sensor, illumination, atmosphere, season, alignment). Change detection only works if the first kind of difference is larger than the second. Every check on this page is a way to make the second kind small, or at least to know how large it is.

The nature of the change you are looking for also shapes how far apart your comparison dates should be:

| Change type | Examples | Typical date spacing | Bitemporal or multitemporal? |
| --- | --- | --- | --- |
| **Abrupt** | Demolition, fire, flood, landslide | Days to months, just before and after the event | Bitemporal is usually enough |
| **Gradual** | Urban sprawl, coastal erosion, glacier retreat, forest degradation | Years | Often multitemporal: the step between two consecutive images may be smaller than the noise |
| **Seasonal (cyclical)** | Leaf-out and leaf-fall, crop cycles, snow cover | Not "change" in most projects | Compare the same season, or model the cycle with a time series |
| **Temporary** | Floodwater, construction sites, events | Short, matched to the duration | Bitemporal, timed carefully |

Abrupt changes produce large spectral differences even between closely spaced dates, which makes them the easiest to detect. Gradual changes are hard to separate from noise in a single before/after pair, which is why they often need a longer time series (**{term}`multitemporal analysis <Multitemporal Analysis>`**) rather than one comparison (**{term}`bitemporal analysis <Bitemporal Analysis>`**). The two case studies in this lesson are both bitemporal: eight years of city building in Egypt, and three years of house building in Japan.

---

## 3. A comparability checklist

### A. Co-registration

If two images are not precisely aligned pixel to pixel, **{term}`co-registration <Co-registration>`** errors alone produce false changes, sometimes even from sub-pixel offsets, especially along high-contrast edges like roads and building corners. The figure below shows the effect: an image compared with an exact copy of itself, shifted by a single pixel.

:::{figure} images/coregistration_shift.png
:alt: Three panels. Left, a Sentinel-2 true-colour image of a planned city district. Centre, the change magnitude between that image and a copy shifted by one pixel, with bright lines along every road and building edge. Right, the real change between 2018 and 2026 on the same colour scale.
:width: 100%
:align: center

*A one-pixel (10 m) shift creates "change" along every edge, although nothing changed on the ground (centre). It is weaker than the real eight-year change here (right), but it falls exactly where real change also concentrates: along roads and building outlines. Sentinel-2 data, Cairo, used on the next page.*
:::

Accurate co-registration is a prerequisite for any of the methods in this lesson, not an optional refinement. Data from the same provider and processing chain are usually well aligned; combining sources (for example a satellite image with an aerial image, or two different processing versions) is where misalignment typically appears. For very high-resolution aerial imagery, **view geometry** adds a second alignment problem: tall objects lean away from the image centre, and the direction of the lean depends on the flight path. A building can therefore appear shifted between two acquisitions even when both images are perfectly georeferenced at ground level.

### B. Atmospheric and illumination effects

Haze, cloud shadows, sun elevation angle and view geometry all alter pixel values without any real surface change. At high resolution, shadows are the most visible case: the same building casts a longer shadow in winter than in summer, and a pixel-wise method flags the difference. Atmospheric correction (converting raw sensor values to surface reflectance) and **{term}`radiometric normalization <Radiometric Normalization>`** between dates both reduce these effects. The simplest form of normalization, centring the difference image on its median, appears on the next page: there, the open desert as a whole is slightly brighter in one year than the other.

### C. Seasonal and phenological variation

**{term}`Phenology <Phenology>`**, the cyclical, seasonal change in vegetation, explains why a forest looks markedly different in summer and winter, entirely apart from any land-cover change. Comparing images from similar seasons, or using spectral indices less sensitive to phenological state, keeps this variation from swamping genuine change signals. In arid regions, irrigation creates a related problem: a field or park can be green or brown depending on whether it was watered that month.

### D. Mixed pixels and spatial resolution

A **{term}`mixed pixel <Mixed Pixel>`** contains more than one land-cover type within its footprint, for example a 10 m Sentinel-2 pixel covering part of a roof, part of a garden and part of a street. If a single small structure appears within that pixel, the spectral change may be too small to detect at all.

:::{figure} images/resolution_mixed_pixels.png
:alt: The same row of new houses shown at 20 cm, 2 m and 10 m pixel size. At 20 cm individual houses and solar panels are visible; at 2 m houses are blurry blocks; at 10 m the scene is a grid of mixed grey pixels.
:width: 100%
:align: center

*The same new houses at three pixel sizes. At 20 cm ({term}`VEXCEL` aerial imagery, used on page 5), each house covers thousands of pixels. At 10 m, the resolution of Sentinel-2, a house is smaller than one pixel and every pixel mixes roofs, gardens and streets.*
:::

Higher-resolution imagery resolves finer changes, but at the cost of larger data volumes, greater sensitivity to the co-registration and view-geometry problems from step A, and more "irrelevant" change such as moved cars and different shadows. Match the resolution to the size of the change you need to detect: whole districts are visible at 10 m (next page), individual houses need sub-metre imagery (page 5).

```{admonition} Ask this before running any method
:class: tip
"Are these two images actually comparable?" is not a rhetorical question. Check co-registration, note the acquisition dates, season and any obvious atmospheric or illumination differences, and consider whether your target change would even be visible at your imagery's resolution, before interpreting any difference as real.
```

---

## 4. Common pitfalls

- **Comparing images from different seasons without accounting for phenology.** This is one of the most common sources of false "change" in practice.
- **Assuming downloaded imagery is already co-registered.** Verify alignment rather than assuming it, especially when combining data from different sources or processing pipelines.
- **Ignoring view geometry in very high-resolution imagery.** Leaning buildings and different shadows can look like change to a pixel-wise method.
- **Choosing a bitemporal comparison for a gradual change process.** A single before/after pair may not reliably separate gradual change from noise; consider whether a longer time series is needed.
- **Ignoring resolution limits for small-scale changes.** If your target change is smaller than a pixel, no method will reliably detect it without higher-resolution data.

---

## 5. Mini task

You want to detect new houses in a suburb using two Sentinel-2 images (10 m resolution) taken one year apart, one in summer and one in winter. List two specific comparability concerns from this page that apply here, and how you would address each.

:::{dropdown} Sample solution
:class: note

First, the summer-versus-winter mismatch introduces phenological variation (deciduous trees with and without leaves, different crop and lawn states) and different shadow lengths, which could be mistaken for change. Using two images from the same season, possibly from different years, would address this, and an index less sensitive to phenology would help further.

Second, at 10 m resolution an individual house is about the size of one pixel or smaller, so it produces mixed pixels (see the resolution figure above). A single new house might change a pixel's values only slightly and not be reliably detectable at all. If counting individual houses is the goal, very high-resolution aerial or satellite imagery is the better source; Sentinel-2 is better suited to detecting whole new neighbourhoods.
:::

---

## 6. Key takeaways

- Two images differ because the surface changed *and* because the observation changed; change detection works only if the first is larger than the second.
- Change type (abrupt, gradual, seasonal, temporary) shapes how far apart your comparison dates should be, and whether bitemporal or multitemporal analysis fits better.
- Co-registration errors alone produce false changes along edges; even a one-pixel shift is visible. At very high resolution, view geometry and shadows add further apparent change.
- Atmospheric, illumination and phenological variation alter pixel values without real surface change, and need to be reduced or accounted for.
- Mixed pixels limit what a given resolution can detect; match the resolution to the size of your target change.

### Further reading

- Esri, ["How Change Detection Works"](https://developers.arcgis.com/python/latest/guide/how-change-detection-works/) — how a deep learning change detector expects its before/after image pairs to be prepared.
- {abbr}`ESA (European Space Agency)`, ["Sentinel-2 User Guide"](https://sentiwiki.copernicus.eu/web/s2-mission) — acquisition, resolution and processing levels of Sentinel-2, useful when planning which dates to compare.
