---
site:
  outline_maxdepth: 2
---

# When to use regressions

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Recognising when you need a continuous value per pixel, not a class
</div>
<!-- markdownlint-enable MD033 -->

---

The landing page introduced regression as predicting "how much" rather than "which class." This page surveys where that fits in practice, and where classification or segmentation, covered in earlier lessons, remain the better choice.

---

## 1. Motivation

A continuous target forced into categorical bins loses information you often cannot recover later. Deciding early whether your project's actual target is continuous, and whether you can realistically obtain reference data to train on, shapes everything from data preparation through evaluation in the rest of this lesson.

---

## 2. Core idea

{term}`Pixel-level regression <Pixel-Level Regression>` fits when your target genuinely varies continuously across a landscape, and when the specific value, not just a category it falls into, is what your project needs. It requires reference data: measurements of the actual target quantity, paired with imagery, to learn from.

---

## 3. Where regression applies

### A. Vegetation and index prediction

**{term}`NDVI <Normalized Difference Vegetation Index>`** and other spectral indices can be predicted directly from multispectral imagery, useful for filling temporal gaps caused by cloud cover, or for harmonizing values across different sensors. This is the running case study for the rest of this lesson.

### B. Structural and physical quantities

{term}`Canopy height <Canopy Height>`, building height, and above-ground biomass are all physical quantities usually measured directly (LiDAR, field surveys) only in limited areas. A regression model trained on these direct measurements, paired with more widely available optical imagery, can produce wall-to-wall estimates across areas where direct measurement is unavailable, extending a geographically limited reference dataset much further.

### C. Environmental and social quantities

Soil moisture, soil organic carbon, and population density are all continuous quantities that can be estimated from imagery combined with other spatial variables, supporting applications from precision agriculture to demographic analysis.

### D. When other approaches are better

If your target genuinely has a small number of meaningful, distinct categories, and the exact value within a category adds little practical value, classification or segmentation (L05 and L07) are usually simpler and more directly interpretable. Land cover with a handful of well-defined classes is a good example: "forest" versus "cropland" is often more useful to a downstream user than a continuous greenness value, unless that specific continuous detail is what the project actually needs.

```{admonition} A quick mental test
:class: tip
Ask: "If I had the exact continuous value at every pixel, would that be more useful to my project than a handful of categories?" If yes, and if you can obtain or approximate reference measurements to train on, regression is a strong candidate.
```

---

## 4. Python reactivation

No new Python here. This page is about scoping the target variable and confirming reference data exists, before the next page gets into how the modeling task itself changes.

---

## 5. Common pitfalls

- **Forcing a continuous target into artificial categories out of habit.** If your target is genuinely continuous and reference data supports regression, discretizing it early throws away information you may need later.
- **Assuming regression fits without confirming reference data exists.** Unlike some classification tasks, regression specifically needs paired, continuous reference measurements; without them, there is nothing to train on.
- **Choosing regression when only a rough category is actually needed.** Added modeling complexity is not automatically worth it if your project's real question is coarser than a precise continuous value.

---

## 6. Mini task

Your project estimates wildfire fuel load across a forested region. Would you frame this as classification, segmentation, or regression? What reference data would you need, and where might you realistically obtain it?

:::{dropdown} Sample solution
:class: note

Fuel load is a continuous physical quantity (typically measured in mass per unit area), so regression is the natural framing, provided reference measurements are available. Realistic reference data sources include field plot measurements from forestry agencies, LiDAR-derived biomass estimates, or existing fuel-load models used by fire management agencies; the imagery (optical or radar) would then be paired with these reference values to train the regression model, the same overall pattern used for canopy height and biomass estimation.
:::

---

## 7. Key takeaways

- Regression fits genuinely continuous targets, where the specific value matters more than a category, and requires paired reference measurements to train on.
- NDVI, canopy height, biomass, soil moisture, and population density are common geospatial regression targets.
- Regression extends geographically limited reference data (LiDAR, field surveys) to wall-to-wall coverage using more widely available imagery.
- Classification or segmentation remain the better choice when a small number of meaningful categories genuinely answers the project's question.

### Further reading

- [Canopy Height Estimation with GeoAI and Pretrained Models](https://youtu.be/vsIRTM98qaU) — a video walkthrough of a canopy-height regression workflow, one of the applications introduced above.
