---
site:
  outline_maxdepth: 2
---

# When segmentation fits

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Choose pixel-level classification when the spatial extent of a feature matters
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this decision matters

Segmentation, detection, and classification each demand a different kind of {term}`annotation <Annotation>`, a different training setup, and answer a different kind of question. Picking the wrong one means discovering the mismatch only after tiling data, training a model, and evaluating results, which is an expensive way to learn that you needed a different task all along.

---

## 2. Core idea

Semantic segmentation fits naturally when your target is best described as an *area* or a *continuous surface* rather than a countable set of discrete things:

- Buildings as a footprint mask, rather than one box per building.
- Water bodies, with their irregular, often branching boundaries.
- Cloud and cloud-shadow masks, needed as a preprocessing step before almost any other imagery analysis.
- Vegetation cover, and full land-cover classification into many thematic classes.

```{admonition} A quick mental test
:class: tip
Ask yourself: "Do I need to know exactly which pixels belong to this class, or would knowing roughly where a handful of discrete objects are be enough?" If you need the actual shape and extent, segmentation fits. If you mainly need to count or locate discrete items, revisit L06 instead.
```

Segmentation is *not* enough when your project genuinely needs to separate individual objects of the same class. A segmentation model happily labels every pixel in a parking lot as "car," but it will not tell you that there are 14 separate cars, since it has no notion of individual instances. If counting or tracking individual objects matters, you need either {term}`object detection <Object Detection>` (L06) or {term}`instance segmentation <Instance Segmentation>` (L08), which combines both ideas.

Consider an aerial image containing buildings, vegetation and roads. A segmentation model could produce:

```text
input imagery
      ↓
┌─────────────────────┐
│ building  vegetation│
│ building  vegetation│
│ road      road      │
│ road      building  │
└─────────────────────┘
      ↓
class value for every pixel
```

The resulting {term}`Raster Mask` preserves the image grid and can therefore represent irregular boundaries much more naturally than bounding boxes.

---

## 3. Match the task to the question

A useful starting point is to ask what one model prediction should represent.

| Research need | Suitable task | Typical output |
| --- | --- | --- |
| What kind of scene is this? | {term}`Image Recognition` | One label per image |
| Where are individual objects? | {term}`Object Detection` | Bounding box per object |
| Which class belongs to every pixel? | Semantic segmentation | One class per pixel |
| Which pixels belong to each individual object? | {term}`Instance Segmentation` | Separate mask per object |

### A. Use it for area-like features

Surface water is a good example.

A lake or river can have a complex boundary. A bounding rectangle includes large areas of land that are not water.

A binary segmentation mask can instead represent:

```text
0 = background
1 = water
```

This makes the result suitable for estimating water extent, examining shoreline patterns or producing a raster mask for later analysis.

### B. Use it when boundaries matter

Buildings can also be represented as segmentation masks.

This is useful if your question concerns:

- built-up area,
- footprint geometry,
- spatial coverage.

A building mask can follow the structure more closely than a detection box.

### C. Use multi-class for thematic maps

Land-cover mapping is a typical multi-class task.

Each pixel might be assigned to a category such as:

```text
water
forest
cropland
impervious surface
wetland
```

The result resembles a conventional thematic raster map.

### D. Use it for preprocessing masks

Not every segmentation result is a final research output.

Cloud and cloud-shadow masks are an important example. They can be used to identify pixels that should be excluded before another remote-sensing analysis.

---

## 4. When segmentation is not enough

Semantic segmentation knows **what class each pixel belongs to**, but not necessarily **which individual object it belongs to**.

Imagine two adjacent buildings.

A semantic mask might contain two buildings are simply labelled `building`. If their boundaries touch in the mask, you may not be able to recover two separate building identities reliably.

This matters when the project needs:

- object counts,
- one measurement per individual feature,
- separate object identities,
- overlapping objects.

Instance segmentation is often better suited to those questions.

```{important}
Semantic segmentation answers **what is at each pixel?** It does not inherently answer **which individual object does this pixel belong to?**
```

---

## 6. Reference data are needed

Semantic segmentation needs pixel-level {term}`Ground Truth`. Reference labels may come from:

- existing raster classifications,
- manually drawn masks,
- rasterized vector polygons,
- field or survey products,
- authoritative spatial datasets.

However, reference data may contain errors relating to position, outdated boundaries and inconsistent class definitions. If a building polygon is shifted relative to the aerial image, the model is effectively asked to learn the wrong boundary.

```{tip}
Overlay imagery and reference labels before training. Misalignment that looks small on a map can affect many pixels along object boundaries.
```

---

## 7. From segmentation to spatial analysis

A segmentation model normally returns a raster prediction. That raster can itself be the final result, or it can become an intermediate product:

```text
imagery
   ↓
segmentation
   ↓
raster mask
   ↓
optional post-processing
   ↓
vectorization / area statistics / overlay analysis
```

For instance, a water mask can be converted into polygons; cloud masks can be used to define pixels that can be used; and land-cover classes can be summarised by administrative unit. Your required downstream analysis should influence how you design the segmentation task.

---

## 8. Common pitfalls

- **Choosing segmentation when the real question is a count.** If your deliverable is "how many X," segmentation alone will not answer it without an extra object-separation step.
- **Choosing detection when the real question is area or shape.** A bounding box is a poor proxy for actual object boundaries; do not approximate an area estimate from box dimensions when a mask would give you the real shape.
- **Ignoring how discrete your classes actually are.** Some "objects," like sprawling wetlands or informal settlements, do not have a natural single-instance boundary at all, which makes them segmentation targets even though they might sound like discrete features at first.

---

## 9. Mini task

Take three example targets: (1) individual solar panels on a rooftop, (2) total solar panel coverage area on a rooftop, (3) impervious surface across a neighborhood. For each, decide whether classification, detection, segmentation, or instance segmentation fits best, and justify your choice in one sentence.

:::{dropdown} Sample solution
:class: note

1. Individual solar panels: object detection, since the goal is to locate and count discrete panels.
2. Total solar panel coverage area: semantic segmentation, since the deliverable is an area, not a count, and a mask directly measures covered pixels.
3. Impervious surface across a neighborhood: semantic segmentation, since impervious surface is a continuous, non-discrete class with no natural "one object" boundary.
:::

---

## 10. Key takeaways

- Semantic segmentation fits area-like, continuous targets where the deliverable is a thematic map, not a count or a list of objects.
- It is not sufficient on its own when individual object separation or counting matters; that need points to detection (L06) or instance segmentation (L08).
- The same physical feature (a building) can call for detection, segmentation, or instance segmentation depending on exactly what the project needs from it.
- The next page introduces how a segmentation model actually turns an image into a mask.

### Further reading

- IBM, ["What is Semantic Segmentation?"](https://www.ibm.com/think/topics/semantic-segmentation) — a general, non-geospatial introduction covering the same task distinctions above.
- CVAT, ["What is Semantic Segmentation & How Does it Work?"](https://www.cvat.ai/resources/blog/what-is-semantic-segmentation) — a practical, annotation-focused perspective on the same task.
