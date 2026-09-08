---
site:
  outline_maxdepth: 2
---

# Scaling segmentation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Scaling segmentation across large rasters with batching and tiling
</div>
<!-- markdownlint-enable MD033 -->

---

Every workflow so far has processed one image at a time. This page covers two different ways to scale up: batch segmentation across many separate images, and tiled segmentation for a single raster too large to process in one pass.

---

## 1. Motivation

Real projects rarely involve just one image. Whether you have many separate scenes or one large raster that exceeds GPU memory, the right scaling approach depends on which situation you are actually in, and this page covers both.

---

## 2. Core idea

Batch segmentation processes multiple separate images with the same prompt in one workflow, useful when you have many images to cover. {term}`Tiled inference <Tiled Inference>` divides one large image into overlapping windows, processes each independently, and merges the results, useful when a single image exceeds available {term}`GPU <GPU>` memory. These solve different problems and are not interchangeable.

---

## 3. Workflow

### A. Batch segmentation

```{code-cell} python
image_paths = []
for i in range(1, 5):
    url = f"https://data.source.coop/opengeos/geoai/uc-berkeley-{i}.tif"
    image_paths.append(download_file(url))

sam3 = SamGeo3(backend="meta", load_from_HF=True, device=None, checkpoint_path=None)
sam3.set_image_batch(image_paths)
sam3.generate_masks_batch("building", min_size=100)
```

`set_image_batch()` and `generate_masks_batch()` are the {term}`batch inference <Batch Inference>` counterparts of `set_image()` and `generate_masks()`, running the same prompt across every image in the list. `min_size=100` filters out detections smaller than 100 pixels, removing small, likely spurious detections before you even inspect the results.

```{code-cell} python
for i, result in enumerate(sam3.batch_results):
    print(f"Image {i + 1}: Found {len(result['masks'])} objects")

sam3.show_anns_batch(ncols=2, show_bbox=True, show_score=True, figsize=(12, 8))

saved_files = sam3.save_masks_batch(output_dir="output/", prefix="building_mask", unique=True)
```

`batch_results` holds each image's detections, letting you check counts per image before committing to a full export. `show_anns_batch()` displays all results in a grid, and `save_masks_batch()` exports every image's masks as separate georeferenced GeoTIFFs in one call.

### B. Tiled segmentation for one image

```{code-cell} python
image_path = download_file("https://data.source.coop/opengeos/geoai/naip_water_train.tif")
geoai.print_raster_info(image_path)

sam = SamGeo3(backend="meta")
sam.generate_masks_tiled(
    source=image_path,
    prompt="water",
    output="segmentation_mask.tif",
    tile_size=1024,
    overlap=128,
    min_size=100,
    unique=False,
    dtype="int32",
    verbose=True,
)
```

`generate_masks_tiled()` divides the input into overlapping `tile_size`-pixel windows, segments each independently, and merges the results into one seamless georeferenced output, the same overlap-and-blend pattern used for tiled inference in earlier lessons, now applied to a prompt-based, zero-shot workflow.

### C. Tuning tile parameters

- **`tile_size`**: larger tiles capture more spatial context but need more GPU memory; start around 1024 and reduce if you hit out-of-memory errors.
- **`overlap`**: higher overlap (128 to 256 pixels) reduces boundary artifacts at the cost of more processing time, the same trade-off from L06 and L07's tiled workflows.
- **`min_size`/`max_size`**: filter out noise (very small detections) or irrelevant large regions.
- **`dtype`**: `int32` for scenes with many objects, `uint16` for up to 65,535, `uint8` for binary masks, the same sizing logic from the previous page's `dtype` choice, now applied at a potentially much larger scale.

### D. Vectorize and smooth the tiled result

```{code-cell} python
geoai.raster_to_vector("segmentation_mask.tif", "segmentation_mask.gpkg")
gdf = geoai.smooth_vector("segmentation_mask.gpkg", "segmentation_mask_smooth.gpkg")
```

`smooth_vector()` softens pixelation artifacts from the raster-to-vector conversion, a lighter-weight alternative to full regularization, well suited to naturally curved features like water bodies where right-angle regularization would not make sense.

---

## 4. Python reactivation

`enumerate(sam3.batch_results)` pairs each result with its position in the list, the same pattern used for indexed iteration throughout this course. The f-string loop building `image_paths` (`for i in range(1, 5): ... image_paths.append(...)`) is a standard accumulation pattern, equivalent to a list comprehension but written as an explicit loop for readability.

---

## 5. Common pitfalls

- **Setting `tile_size` too large for available GPU memory.** Reduce it first if you encounter an out-of-memory error, the same first fix used in L08's Mask R-CNN training.
- **Setting `overlap` too low.** This risks visible seams at tile boundaries, exactly the artifact overlap is meant to prevent.
- **Choosing `dtype` without estimating your object count first.** A large tiled scene can easily exceed `uint8`'s or even `uint16`'s object-ID capacity; check before running a long tiled job.
- **Confusing batch and tiled workflows.** Batch mode is for multiple separate images; tiled mode is for one image too large to process directly. Using the wrong one for your situation wastes effort.

---

## 6. Mini task

You have a single 20,000-by-20,000-pixel satellite scene, far too large to load into GPU memory at once, and you expect a few thousand distinct objects. Would you reach for batch segmentation or tiled segmentation, and what `dtype` would you choose?

:::{dropdown} Sample solution
:class: note

Tiled segmentation fits here, since this is one large image exceeding memory limits, not multiple separate images; batch segmentation would not apply to a single raster at all. For `dtype`, with a few thousand expected objects, `int32` or `uint16` (up to 65,535) would both work; `uint16` is the more memory-efficient choice if you are confident the object count will stay comfortably under its limit, while `int32` provides a safer margin if your object-count estimate is uncertain.
:::

---

## 7. Key takeaways

- Batch segmentation processes multiple separate images with a shared prompt; tiled segmentation processes one image too large for memory in overlapping windows.
- `tile_size`, `overlap`, `min_size`/`max_size`, and `dtype` all trade off against each other; start with reasonable defaults (1024, 128, and a dtype sized to your expected object count) and adjust based on results.
- `batch_results` lets you inspect per-image detection counts before committing to a full export.
- `smooth_vector()` offers a lighter-weight alternative to full regularization, better suited to naturally curved features.

### Further reading

- [GeoAI Tutorial 22: Batch Segmentation on Multiple Satellite Images](https://youtu.be/UHjUoQSdUxg) — a video walkthrough of the batch segmentation workflow covered above.
