---
site:
  outline_maxdepth: 2
---

# When to use it

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Deciding whether one label per image chip is enough
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

Choosing the wrong task formulation wastes real effort, exactly the warning from [L01's core tasks page](../L01/05_core-geoai-tasks.md). Image recognition is attractive because it is quick to set up and train, but it only answers questions of a specific shape. Recognizing that shape early saves you from labeling pixel masks you did not need, or discovering three weeks in that your question actually required object locations.

---

## 2. Core idea

{term}`Image recognition <Image Recognition>` assigns a single categorical label to an entire image tile. It answers "what is the dominant thing in this tile," not "where exactly is it" or "which pixels belong to it."

---

## 3. Workflow

### A. Recognize a good fit

Image recognition fits well when a tile has one dominant, describable content type, and you do not need to know where within the tile that content sits. If you can look at a tile and confidently assign one label without needing to point at a specific pixel or object, this task probably fits.

### B. Look at realistic examples

Regional and national land-use mapping often works this way: classify millions of tiles extracted from a mosaic into categories like forest, cropland, or urban. Crop-type screening across a growing season is another common case. Post-disaster triage can use scene-level labels (undamaged, minor damage, major damage) to prioritize which areas need closer inspection first. Scene screening, filtering out cloudy or irrelevant tiles before running a more expensive model, is a fourth pattern.

### C. Recognize when it is not enough

If your question needs *where* something is, individual buildings, vehicles, or field boundaries, you need {term}`object detection <Object Detection>` (covered in [L06](../06_object-detection.md)) rather than a single tile-level label. If it needs a full pixel-level map, every pixel classified as building, road, or vegetation, you need {term}`semantic segmentation <Semantic Segmentation>` (covered in [L07](../07_semantic-segmentation.md)). Image recognition on a mixed-content tile can still run, but the single predicted label will hide most of what is actually happening in that tile.

### D. Use it as a first, cheap pass

Even when your real question needs a heavier task, image recognition can still help as a pre-filtering step: quickly screening out tiles that clearly do not contain what you care about, before spending compute on detection or segmentation for the tiles that remain.

### E. Define the chip carefully

The chip size affects the meaning of the label. A 64 × 64 Sentinel-2 chip may represent a broad land-use pattern. A high-resolution aerial chip of the same pixel size may show only a few buildings or trees.

### F. Think about mixed scenes

Many geospatial image chips contain more than one land-cover type. A “residential” chip may include roads, gardens, trees and rooftops. A “river” chip may include water, vegetation and bridges.

This is not automatically a problem, but the class definition must be clear. In many classification datasets, the label refers to the dominant or most representative scene type rather than a pure land-cover class.

```{admonition} A quick self-check
:class: hint
Ask yourself: "If I labeled this tile with one word, would that word actually capture what I need to know?" If yes, image recognition is a reasonable starting point. If you find yourself wanting to add "...and also there are three of them in the top-left corner," you need a different task.
```

---

## 4. Common pitfalls

- **Forcing a mixed-content tile into a single label.** A tile that is half forest, half cropland does not have one honest label; consider a smaller tile size or a different task.
- **Confusing scene-level classification with pixel-level land cover mapping.** These sound similar but are structurally different tasks, as you saw in [L01](../L01/05_core-geoai-tasks.md); pixel-level mapping is functionally segmentation.
- **Choosing image recognition purely because it is faster to set up.** Faster setup does not help if the resulting label does not actually answer your project's question.

---

## 5. Mini task

For each rough project prompt below, decide whether image recognition fits, and if not, name the task that would fit better.

1. "I want to flag which satellite tiles in my study area show visible flooding."
2. "I want to count individual solar panels on rooftops across a city."
3. "I want a map showing exactly which pixels are impervious surface versus vegetation."

:::{note} Sample solution
:class: dropdown

1. Fits: a "flooded / not flooded" scene label is a natural single-label question per tile.
2. Does not fit: counting individual objects needs locations, which is object detection.
3. Does not fit: a pixel-level map is semantic segmentation, not a single tile-level label.
:::

---

## 6. Key takeaways

- Image recognition assigns one label to a whole tile; it does not localize objects or classify individual pixels.
- It fits questions like land-use screening, crop-type triage, disaster scene triage, and pre-filtering before a heavier task.
- It does not fit questions that need object locations (detection) or pixel-level maps (segmentation).
- A quick self-check, "would one label per tile actually answer my question," catches most mismatches early.

## 7. Further reading

- [What Is Image Classification? (IBM)](https://www.ibm.com/think/topics/image-classification) — a general, non-geospatial overview of image classification use cases and how it fits within computer vision more broadly.
- [Course02_Image_Classification_Colab (carto.ma)](https://carto.ma/Course02_Image_Classification_Colab.html) — a hands-on, Colab-based walkthrough of image classification fundamentals, useful if you want a second worked example outside the geospatial context of this course.
