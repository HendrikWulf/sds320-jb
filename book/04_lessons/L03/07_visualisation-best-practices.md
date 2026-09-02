---
site:
  outline_maxdepth: 2
---

# Best practices

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Colormap choice, spatial context, consistent styling, and knowing your audience
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

A technically correct map can still be a poor map. A misleading colormap, a missing basemap, or an unlabeled layer can make a viewer misread your results, even when the underlying data and analysis are sound. These habits also feed directly into the [figures and maps](../03_project/08_figures-and-maps.md) guidance in your Project handbook.

---

## 2. Core idea

Good visualization is about choosing representations that match your data type and communicate correctly to your intended audience.

---

## 3. Workflow

### A. Match the colormap to the data type

Sequential {term}`colormaps <Colormap>` such as `"batlow"` or `"viridis"` suit continuous data with a natural low-to-high ordering, such as elevation. Diverging colormaps such as `"RdBu"` suit data with a meaningful center point, such as a change value or a temperature anomaly. Categorical data, such as {term}`land cover classification <Land Cover Classification>`, needs a qualitative colormap with visually distinct colors per class. Avoid rainbow colormaps: they lack a natural perceptual ordering and can make differences look larger or smaller than they really are.

### B. Include spatial context

A prediction map without context is difficult to interpret. A cluster of polygons floating in empty space does not mean much on its own. Include enough context (e.g. basemap, boundaries) for the reader to know where the result is and what it relates to.

### C. Keep comparisons consistent

When comparing outputs from different experiments, dates, or models, use the same colormap, value range, and opacity across every layer in the comparison. Inconsistent styling can create the appearance of a difference that is not actually present in the data.

### D. Label your layers clearly

Give each layer a descriptive name in the layer control. `"Building Predictions (U-Net)"` tells a reader something useful; `"Layer 1"` does not, especially once you have several layers active at once.

### E. Design for your actual audience

A technical peer may appreciate a detailed, multi-layer interactive map. A non-technical reader, or your instructor reading a final report, often needs a simpler view with a clear legend and a short caption explaining what they are looking at.

| Audience | Map style |
| --- | --- |
| You during analysis | Interactive, layered, includes debugging information. |
| Tutor or peer feedback | Shows relevant comparison and highlights uncertainty. |
| Final report reader | Clean, focused, labelled and explained in a caption. |
| Non-technical audience | Fewer layers, clear legend, limited jargon. |

---

## 4. Key takeaways

- Match colormap type (sequential, diverging, categorical) to your data type; avoid rainbow colormaps for continuous data.
- Always include a basemap or reference layer for spatial context.
- Keep styling consistent across any layers you intend to compare.
- Label layers descriptively, and design the level of detail around your actual audience.
