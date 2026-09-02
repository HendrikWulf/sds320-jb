---
site:
  outline_maxdepth: 2
---

# Segmentation concepts

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Understand masks, class values, probability maps and large-image inference
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

All the segmentation functions that you will use in this lesson — training, inference and evaluation — operate on {term}`masks <Mask>`  with specific shape and value conventions. Getting comfortable with these conventions now will save you time later.

---

## 2. Core idea

A segmentation model performs image-to-mask prediction: given an input image with height H and width W, it outputs a raster of the same H and W, where every pixel holds a predicted class index. This is fundamentally different from classification (one label for the whole image) and detection (a variable-length list of boxes); segmentation always produces exactly one prediction per pixel, with no separate step for describing objects or entire scenes.

---

## 3. Working with masks

### A. Binary and multi-class masks

A **binary mask** has two possible pixel values: typically 0 for background and 1 for the target class (building, water, and so on).

```text
0 = background  (not tree)
1 = target   (tree)
```

A **multi-class mask** extends this to more than two values, one integer per class, for example 0 through 12 for a 13-class land-cover scheme, with 0 conventionally reserved for background or "no data."

For example:

```text
0 = background
1 = water
2 = forest
3 = cropland
4 = impervious surface
```

```{tip}
Always check your mask's actual pixel values before training, using a quick histogram or `numpy.unique()` on the array. A mask that should be binary but contains unexpected values (e.g. 255 instead of 1) is an easy issue to detect.
```

### B. Probability maps

In addition to a hard class prediction, a segmentation model can also produce a **probability map**: a multi-band raster where each band corresponds to one class, and pixel values range from 0 to 1, representing the model's confidence that a given pixel belongs to that class. This is the pixel-level equivalent of the {term}`confidence score <Confidence Score>` you used per-detection in L06.

For a two-class example:

```text
pixel:
background → 0.08
tree   → 0.92
```

A probability map is useful for spotting uncertain regions, for example along class boundaries, and for setting a custom confidence threshold rather than accepting the model's default hard classification.

### C. Sliding-window inference

Real rasters are almost always larger than a model's expected input size. {term}`Tiled inference <Tiled Inference>` (also called sliding-window inference in this context) solves this the same way it did in L06: a fixed-size window slides across the full raster with some overlap between neighboring windows, the model predicts each window independently, and the overlapping predictions are stitched into one seamless output raster. The overlap smooths out any inconsistency at window edges, the same purpose it served for detection tiling.

| parameter | explanation |
| --- | --- |
| Tile size | Defines the dimensions of each model input (e.g., 512 × 512 pixels). Larger tiles provide more spatial context but require more memory. |
| {term}`Stride` | The distance the window moves between neighboring tiles during training. For example, a tile size of 512 with a stride of 256 creates a 50% overlap. |
| Overlap | Overlapping windows used during inference to reduce visible seams and improve prediction quality near tile edges before stitching them into a continuous raster. |

### D. Raster predictions and vector outputs

A model's direct output is always a {term}`raster <Raster Data>`: a grid of class values aligned with the input imagery. For many downstream uses, especially GIS workflows, {term}`vector <Vector Data>` polygons are more useful, for example to compute an area, join with other vector layers, or filter by shape. Converting a raster mask into vector polygons is a separate, deliberate post-processing step, not something the model does automatically; you will practice this conversion on the binary-workflow page. Vectorization does not make the original prediction more accurate. It only changes its representation.

---

## 4. Python reactivation

A mask, once loaded, is a NumPy array, the same data structure you have used for raster bands in earlier lessons. `array.shape` tells you its height and width; `numpy.unique(array)` lists the distinct values actually present, which is exactly the quick check recommended above before training on a new dataset.

You can count target pixels with a Boolean expression:

```{code-cell} python
building_pixels = (mask == 1).sum()

building_pixels
```

The same logic scales to large raster masks.

For a multi-class mask:

```{code-cell} python
classes, counts = np.unique(mask, return_counts=True)

list(zip(classes, counts))
```

This combines familiar array operations with the output of a deep-learning model.

---

## 5. Common pitfalls

- **Assuming mask values without checking them.** Different datasets use different conventions (0/1, 0/255, or arbitrary integers); verify before training rather than assuming.
- **Confusing a probability map with the final class mask.** The class mask is a single-band raster of class indices; the probability map is a separate, multi-band raster of per-class confidence. They serve different purposes and are not interchangeable.
- **Treating tiled inference edges as guaranteed to be perfect.** Overlap reduces boundary artifacts but does not eliminate them entirely; visually check a few tile boundaries in your output before trusting it fully.
- **Forgetting that raster-to-vector conversion is a choice, not a default.** Decide early whether your project deliverable needs vector output, since it changes what post-processing steps you plan for.

---

## 6. Mini task

A colleague shows you a segmentation mask array with `array.shape == (512, 512)` and `numpy.unique(array)` returning `[0, 1, 2, 3, 4]`. Without seeing the imagery, what can you conclude about the task, and what would you ask before trusting the mask?

:::{dropdown} Sample solution
:class: note

The shape confirms a single-band, 512-by-512 raster, consistent with a class-index mask rather than a probability map (which would have multiple bands). Five distinct values means this is a multi-class mask with five classes (commonly 0 as background plus four target classes, though that assumption should be confirmed). Before trusting it, you would want to know what each integer value actually represents, since without a class-name mapping, "class 3" is meaningless, and whether 0 truly represents background or is itself a valid class in this particular dataset.
:::

---

## 7. Key takeaways

- A segmentation model outputs one class prediction per pixel, in a raster matching the input's height and width, unlike a single scene label or a list of boxes.
- Binary masks use two values; multi-class masks use one integer per class, generally with 0 reserved for background.
- A probability map stores per-class confidence at every pixel and is distinct from the final hard class mask.
- Sliding-window (tiled) inference handles rasters larger than the model's input size, the same principle used for detection in L06.
- Converting raster predictions to vector polygons is a deliberate post-processing step, not an automatic part of inference.
