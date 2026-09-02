---
site:
  outline_maxdepth: 2
---

# Core GeoAI tasks

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
The seven task types that define almost every GeoAI problem, and how to tell them apart
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

Picking the wrong task type wastes effort. Using classification when you actually need regression, or segmentation when detection would be sufficient, means relabeling data and retraining later. A carpenter needs to know when to reach for a saw versus a drill; a GeoAI practitioner needs the same instinct for task types.

---

## 2. Core idea

Every GeoAI task differs mainly in what the model outputs: a single label, a set of boxes, a full pixel map, or a continuous value. Once you know what output your question needs, the task type is usually clear.

---

## 3. The seven core tasks

### A. Image recognition

Assigns one categorical label to a whole image chip, for example "residential" or "forested," with no information about *where* within the chip that label applies. It is the simplest granularity, useful as a pre-filter (find which tiles contain buildings before running an expensive segmentation model) or for scene-level inventories. A key challenge is mixed content: a single chip can straddle both urban and agricultural land, and shifting the tiling grid by a few pixels can change which label looks dominant.

> Example: Classify image chips as residential, industrial, forest or agriculture.  
> Use case: This is useful for screening large archives or creating scene-level summaries.

### B. Object detection

Draws {term}`bounding boxes <Bounding Box>` around discrete objects and assigns each one a class and a {term}`confidence score <Confidence Score>`, for example counting vehicles in a parking lot or ships in a harbor. It is the right choice when you need to count or locate individual objects rather than describe the whole scene. A distinctive challenge in overhead imagery is the huge range of object sizes: a vehicle might be a handful of pixels while a building spans hundreds, which is why detection models for this domain often use {term}`feature pyramid networks <Feature Pyramid Network (FPN)>` to handle multiple scales at once.

> Example: Detect ships in a harbour or vehicles in a parking lot.  
> Use case: The output is usually a set of bounding boxes. Detection is useful for counts and locations.

### C. Semantic segmentation

Assigns a class label to every pixel, producing a thematic map (building, road, vegetation, water, bare soil) with the same spatial dimensions as the input. This is one of the most common GeoAI tasks because it directly produces the kind of map that planners and geographers actually use. {term}`U-Net`'s {term}`encoder-decoder architecture <Encoder-Decoder Architecture>`, which compresses an image to learn context and then reconstructs pixel-level detail, is a standard choice here.

> Example: Map water, vegetation, buildings and bare soil for a study area.  
> Use case: This is useful for thematic mapping and area statistics.

### D. Instance segmentation

Combines segmentation and detection: it classifies every pixel *and* distinguishes individual objects, so you know not just that a pixel belongs to a building, but which building. This matters for tasks like building footprint extraction, where adjacent structures of the same type need separate polygons. {term}`Mask R-CNN` is the classic architecture; {term}`SAM <Segment Anything Model (SAM)>` is a more recent, general-purpose alternative.

> Example: Extract separate building footprints or individual tree crowns.  
> Use case: This is useful when counts, shapes or object-level measurements matter.

### E. Image translation

Transforms an image from one representation to another while preserving spatial structure. The most common geospatial version is {term}`super-resolution <Super-Resolution>`, generating a higher-resolution image from a lower-resolution input, though {term}`cloud removal <Cloud Removal>` is another example. A critical limitation: the added detail is *inferred*, not observed. A super-resolution model cannot recover information the sensor never captured; it predicts plausible detail based on patterns it learned elsewhere. That distinction matters if your project needs measurement accuracy rather than just a sharper-looking image.

> Example: Create a super-resolution version of a lower-resolution image.  
> Use case: This can support visualisation or some workflows, but inferred detail must be interpreted carefully.

### F. Change detection

Identifies differences between images of the same location at different times, from a simple binary "changed / not changed" map to a multi-class map of change type (forest to urban, intact building to rubble). Models typically process image pairs through {term}`siamese networks <Siamese Network>` that learn to compare features across the two time steps. The core challenge is separating meaningful change from nuisance variation, differences in lighting, season, or sensor calibration, while staying sensitive to genuine change.

> Example: Identify areas that changed between a pre-event and post-event image.  
> Use case: This requires careful attention to temporal alignment, seasonality, clouds, illumination and sensor differences.

### G. Pixel regression

Predicts a continuous value per pixel rather than a category: canopy height, biomass, soil moisture, elevation. Architecturally this looks like segmentation, an {term}`encoder-decoder <Encoder-Decoder Architecture>` network, but with a regression head and a loss such as mean squared error instead of a classification loss.

> Example: Estimate canopy height, biomass, temperature or soil moisture per pixel.  
> Use case: This task needs suitable reference data and evaluation metrics for continuous values.

```{admonition} A quick decision check
:class: important
Ask yourself three questions in order: (1) Do I need one label for the whole image, or information about *where* things are? (2) If location matters, do I need boxes around discrete objects, or a full pixel-level map? (3) Is my target a category or a continuous number? Those three answers narrow you down to one of the seven tasks almost every time.
```

---

## 4. Choosing between tasks

| Task | Main question | Typical output |
| --- | --- | --- |
| {term}`Image Recognition` | What type of scene or chip is this? | One label or several labels per image chip. |
| {term}`Object Detection` | Where are discrete objects? | Bounding boxes, class labels and confidence scores. |
| {term}`Semantic Segmentation` | What class is each pixel? | Pixel-level classification map. |
| {term}`Instance Segmentation` | Which pixels belong to each individual object? | Separate mask for each object instance. |
| {term}`Image Translation` | How can one image representation be converted into another? | Enhanced, reconstructed or transformed image. |
| {term}`Change Detection` | Where or how did something change over time? | Binary, multi-class or continuous change map. |
| {term}`Pixel-Level Regression` | What continuous value belongs to each pixel? | Continuous raster surface. |

---

## 5. Python reactivation

You will not train a model in this lesson, but it is worth remembering how you already reason about outputs in Python: a single value (like a scene label) behaves like one variable, a list of detections behaves like a list of dictionaries, and a pixel map behaves like a NumPy array the same shape as your input image. Keeping that mental model will make the actual `geoai` output objects easier to read once you reach them.

---

## 6. Common pitfalls

- **Defaulting to segmentation because it feels the most "complete."** If your question only needs a scene-level answer, image recognition is faster to label, train, and evaluate.
- **Choosing instance segmentation when semantic segmentation would answer the question.** If you do not actually need to separate individual objects, plain semantic segmentation is simpler and needs less annotation effort.
- **Forgetting that image translation output is inferred, not measured.** Do not use super-resolved imagery as if it were a genuine higher-resolution observation in a downstream analysis.

---

## 7. Mini task

For each rough project prompt below, name the single best-fitting task type and explain briefly why the alternatives are a worse fit.

1. "I want to estimate how much biomass is stored in a forest area from satellite imagery."
2. "I want to know which individual parking spots are occupied in a large lot."
3. "I want to know how much impervious surface a city gained over the last ten years."

:::{note} Sample solution
:class: dropdown

1. Pixel regression: biomass is a continuous quantity mapped across space, not a category, which rules out segmentation and classification.
2. Object detection: each parking spot needs an individual location and a binary state (occupied or not), which is a natural fit for bounding boxes rather than a full pixel-level map.
3. Change detection: this compares the same area at two points in time and needs the type or extent of change, not just a single-date classification.
:::

---

## 8. Key takeaways

- The seven core GeoAI tasks are image recognition, object detection, semantic segmentation, instance segmentation, image translation, change detection, and pixel regression.
- They differ mainly by output: one label, a set of boxes, a full pixel map, a translated image, a difference map, or a continuous value.
- A short decision check, whole-image vs. located, boxes vs. pixels, category vs. number, narrows most project ideas to one task quickly.
- Choosing the simplest task that actually answers your question saves labeling and training effort later.
-
