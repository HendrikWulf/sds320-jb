---
site:
  outline_maxdepth: 2
---

# Text, point & box prompts

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Prompting SAM with points, boxes and text
</div>
<!-- markdownlint-enable MD033 -->

---

You know conceptually what a prompt does. This page puts all three types into practice: text prompts on a UC Berkeley satellite image, and point prompts on a close-up truck photo, building the practical vocabulary the main building-extraction workflow uses two pages from now.

---

## 1. Motivation

Choosing the right {term}`prompt <Prompt>` type for a task saves real effort. A text prompt can find every building in a scene in one call; a point prompt gives you surgical control over a single object. Knowing which to reach for, rather than defaulting to one out of habit, is a practical skill this page builds directly.

---

## 2. Core idea

Text, point, and box prompts trade breadth against precision. Text prompts are fast and broad, describing a category rather than a location. Point prompts give precise control over a single object, at the cost of needing to know where to click. Box prompts guide the model spatially, useful when you can draw a region but do not want to click an exact point.

---

## 3. Workflow

### A. Text prompts

```{code-cell} python
url = "https://data.source.coop/opengeos/geoai/uc-berkeley.tif"
image_path = download_file(url)

sam3 = SamGeo3(backend="meta", load_from_HF=True, device=None, checkpoint_path=None)
sam3.set_image(image_path)

sam3.generate_masks(prompt="building")
sam3.show_anns()
```

A single word or short phrase segments every matching object in the scene at once, no coordinates required. This is the broadest, lowest-effort prompt type, well suited to a first look at a scene or a common, visually generic category.

```{code-cell} python
sam3.save_masks(output="building_masks.tif", save_scores="building_scores.tif", unique=True)
```

`unique=True` assigns a distinct integer value to each segmented object, the same instance-identifier idea from L08. `save_scores` writes a separate raster storing each pixel's confidence value, letting you filter or inspect low-confidence detections later without discarding the whole result.

### B. Box prompts

```{code-cell} python
boxes = [[-122.2597, 37.8709, -122.2587, 37.8717]]
box_labels = [True]  # True = include, False = exclude

sam3.generate_masks_by_boxes(boxes, box_labels, box_crs="EPSG:4326")
sam3.show_anns()
```

A box drawn around one object tells SAM to use that object as a reference and search for visually similar objects elsewhere in the image, a box around one building often finds the other buildings in the scene too. Boxes are specified in `[xmin, ymin, xmax, ymax]` format; `box_crs` tells `samgeo` which coordinate system your coordinates use, so geographic coordinates work directly without manual conversion.

### C. Point prompts

```{code-cell} python
url = "https://data.source.coop/opengeos/geoai/truck-example.jpg"
image_path = download_file(url)
show_image(image_path, axis="on")

sam = SamGeo3(backend="meta", enable_inst_interactivity=True)
sam.set_image(image_path)

sam.generate_masks_by_points([[750, 370]])
sam.show_points([[750, 370]], [1])
sam.show_anns()
```

`enable_inst_interactivity=True` turns on point- and box-based instance segmentation mode. A single foreground point (`label=1`) selects whichever object is most likely at that pixel location. Adding more foreground points on the same object improves coverage for large or irregular shapes:

```{code-cell} python
sam.generate_masks_by_points([[500, 375], [1125, 625]], point_labels=[1, 1])
```

A **background point** (`label=0`) excludes a region, useful for refining an ambiguous boundary:

```{code-cell} python
sam.generate_masks_by_points([[750, 370], [1125, 625]], point_labels=[1, 0])
```

The foreground point still selects the object; the background point carves the excluded region out of the resulting mask, giving you fine-grained control an initial single-point prompt could not.

### D. Choosing among the three

```{tip}
Start with a text prompt when your target is a common, visually generic category and you want broad coverage in one call. Switch to a box prompt when you can identify one clear example and want SAM to find similar objects elsewhere. Reach for point prompts when you need precise control over a single, specific object, especially when an initial prompt was ambiguous and you need to refine it with additional foreground or background points.
```

---

## 4. Python reactivation

Point and box coordinates are both passed as lists of lists (`[[x1, y1], [x2, y2]]` for points, `[[xmin, ymin, xmax, ymax]]` for boxes), the same nested-list pattern you have used for coordinate data throughout this course. `point_labels` and `box_labels` are separate, parallel lists, matched by position to the coordinates list, so the first label always corresponds to the first point or box, exactly the "parallel lists" pattern you used with `zip()` in earlier lessons.

---

## 5. Common pitfalls

- **Passing pixel coordinates when geographic coordinates were expected, or vice versa.** Box prompts on georeferenced imagery need `box_crs` set correctly; point prompts in interactive instance mode use pixel coordinates unless you are working with the geographic-coordinate methods covered on the next page.
- **Confusing foreground and background labels.** `1` means "this is the object," `0` means "exclude this area"; swapping them produces a confusing, likely nonsensical mask.
- **Expecting a single point to always resolve ambiguity.** Some objects need multiple points, or a mix of foreground and background points, to produce a clean mask; treat an unclear single-point result as a cue to add more prompts, not a failure.
- **Defaulting to text prompts for tasks that need precision.** Text prompts find everything matching a description at once; if you need exactly one specific object and not its neighbors, a point or box prompt gives you the control text alone cannot.

---

## 6. Mini task

You want to segment exactly one specific building in a satellite scene, but a text prompt of "building" also picks up several other buildings you do not want included. What prompt strategy would isolate just the one building you care about?

:::{dropdown} Sample solution
:class: note

A point prompt placed on the specific building of interest, or a box drawn tightly around just that building, would isolate it directly, since both give SAM a spatial anchor rather than a category description that matches every instance. If the point-only result still includes unwanted neighboring structures, adding a background point on the unwanted area would refine the mask further, the same foreground-plus-background refinement demonstrated in step C.
:::

---

## 7. Key takeaways

- Text prompts are fast and broad, matching every instance of a described category in one call.
- Box prompts use one example as a spatial reference to find similar objects elsewhere in the scene.
- Point prompts give precise, single-object control, and can be refined with additional foreground or background points.
- Choosing the right prompt type for the task, rather than defaulting to one, saves real effort in practice.
