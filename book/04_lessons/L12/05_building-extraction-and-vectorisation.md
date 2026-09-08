---
site:
  outline_maxdepth: 2
---

# Building extraction

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Extracting building footprints and vectorising the results
</div>
<!-- markdownlint-enable MD033 -->

---

You know all three prompt types conceptually. This page runs the complete building-extraction pipeline on Washington State imagery: prompting with real geographic coordinates, exporting masks, and cleaning the result into regularized, GIS-ready polygons.

---

## 1. Motivation

This is where every earlier page in this lesson comes together into a deliverable you could actually hand off: not just a segmentation, but a clean vector file ready to load into a GIS. Building footprints are a natural anchor for this, since box and point prompts both work well on their well-defined rectangular shapes.

---

## 2. Core idea

The full pipeline has four stages: prompt with geographic coordinates (points or boxes), generate and save georeferenced masks, convert those masks to vector polygons, and regularize the polygon edges into clean, usable shapes.

---

## 3. Workflow

### A. Point prompts from coordinates

```{code-cell} python
image_path = download_file("https://data.source.coop/opengeos/geoai/wa-building-image.tif")
geojson_path = download_file("https://data.source.coop/opengeos/geoai/wa-building-centroids.geojson")

sam = SamGeo3(backend="meta", enable_inst_interactivity=True)
sam.set_image(image_path)

point_coords_batch = [
    [-117.599896, 47.655345],
    [-117.59992, 47.655167],
    [-117.599928, 47.654974],
    [-117.599518, 47.655337],
]

sam.generate_masks_by_points_patch(
    point_coords_batch=point_coords_batch,
    point_crs="EPSG:4326",
    output="masks.tif",
    dtype="uint8",
)
```

`generate_masks_by_points_patch()` segments the building at each coordinate and writes the results as one georeferenced GeoTIFF. `dtype="uint8"` is enough for up to 255 distinct objects; more objects need `uint16` (up to 65,535), a limit worth checking against your actual building count before running a large batch.

### B. Point prompts from a GeoJSON file

```{code-cell} python
sam.generate_masks_by_points_patch(
    point_coords_batch=geojson_path,
    point_crs="EPSG:4326",
    output="building_masks.tif",
    dtype="uint16",
)
```

Rather than typing coordinates manually, passing a GeoJSON path directly lets `samgeo` read every point geometry from the file and process them all in one call, the practical version of step A for a real building-centroid dataset rather than a handful of manually chosen points.

### C. Box prompts from a vector file

```{code-cell} python
boxes_path = download_file("https://data.source.coop/opengeos/geoai/wa-building-bboxes.geojson")

output_masks = "building_masks.tif"
sam.generate_masks_by_boxes_inst(
    boxes=boxes_path,
    box_crs="EPSG:4326",
    output=output_masks,
    dtype="uint16",
    multimask_output=False,
)
```

Just as with point prompts, a vector file of bounding boxes can be passed directly rather than typed out, with `samgeo` extracting each feature's geometry as a separate box prompt. `multimask_output=False` returns one best mask per box rather than several candidates, a reasonable default once you trust your box prompts are already well-targeted.

### D. Convert to vector and regularize

```{code-cell} python
output_vector = "building_vector.geojson"
raster_to_vector(output_masks, output_vector)

output_regularized = "building_regularized.geojson"
regularize(output_vector, output_regularized)
```

`raster_to_vector()` performs the same {term}`vectorization <Vectorization>` step you have used in earlier lessons, converting the raster mask into one polygon per detected object. `regularize()` is `samgeo`'s version of {term}`orthogonalization <Orthogonalization>` from L07: it adjusts jagged, pixel-aligned raster-derived boundaries into cleaner, more regular building footprints, better representing how buildings actually look than an unregularized raster-to-vector conversion would.

```{tip}
Always visually compare the regularized output against the original masks before treating it as final. Regularization improves visual quality, but it is still a geometric approximation, not a guarantee that every corner matches the true building outline.
```

---

## 4. Python reactivation

`point_coords_batch` is a list of two-element lists, the same nested-list pattern from the previous page, here holding longitude-latitude pairs instead of pixel coordinates. Passing a file path (`geojson_path`) in place of that list, as in step B, works because `samgeo` checks the input type and reads coordinates from the file automatically, a convenience pattern you have seen elsewhere in this course where a function accepts either an in-memory object or a path to load one from.

---

## 5. Common pitfalls

- **Choosing `dtype="uint8"` for a scene with more than 255 objects.** Object IDs beyond that range will not be representable; check your expected object count before choosing.
- **Trusting regularized output without a visual check.** As emphasized above, regularization is an approximation; spot-check it against the source imagery before treating it as a finished deliverable.
- **Setting `multimask_output=False` before your box prompts are well-targeted.** While you are still refining prompts, keeping multiple candidate masks (`True`) can help you see where a box prompt is ambiguous; switch to a single best mask once you trust the input.
- **Forgetting `point_crs`/`box_crs` when coordinates are geographic.** Without it, `samgeo` cannot correctly interpret longitude-latitude pairs as real-world locations.

---

## 6. Mini task

You have a GeoJSON file with 800 building centroids for a large study area. Which `dtype` would you choose for the output mask, and why does this matter more here than it did for the four-point example in step A?

:::{dropdown} Sample solution
:class: note

`dtype="uint16"` is the right choice, since it supports up to 65,535 distinct object IDs, comfortably covering 800 buildings, while `uint8`'s 255-object limit would be exceeded partway through the batch. This mattered less for the four-point example in step A, where `uint8` was more than sufficient, but choosing a dtype without checking your actual object count against its limit is a mistake that only shows up once you run a genuinely large batch.
:::

---

## 7. Key takeaways

- `generate_masks_by_points_patch()` and `generate_masks_by_boxes_inst()` both accept either manually specified coordinates or a vector file path directly.
- `dtype` limits how many distinct objects a mask raster can represent; check your expected count before choosing.
- `raster_to_vector()` performs standard vectorization; `regularize()` is `samgeo`'s equivalent of the orthogonalization step from L07, cleaning jagged boundaries into regular shapes.
- Regularized output is a geometric approximation, not a guarantee of accuracy; a visual check against source imagery remains necessary.
