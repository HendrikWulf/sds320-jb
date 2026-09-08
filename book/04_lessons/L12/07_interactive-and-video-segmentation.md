---
site:
  outline_maxdepth: 2
---

# Interactive segmentation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Interactive segmentation and extending SAM to video
</div>
<!-- markdownlint-enable MD033 -->

---

Every workflow so far has run through code, one prompt at a time. This page covers two extensions: a map-based interface that needs no code at all, and video segmentation, which extends prompting and the streaming memory from earlier in this lesson across time.

---

## 1. Motivation

Not every use of SAM needs a script. Interactive, no-code exploration is often the fastest way to test whether a feature is segmentable at all, before committing to a coded workflow. Video segmentation, while more specialized, is directly useful for monitoring applications and makes the streaming-memory concept from earlier in this lesson concrete.

---

## 2. Core idea

`show_map()` combines a live map interface with SAM inference, letting you type a prompt or draw a box directly on the map and see results immediately, without writing a single line of segmentation code. `SamGeo3Video` extends the same prompting ideas, text and point prompts, object identity, confidence scores, from a single image across every frame of a video, using the streaming memory mechanism from earlier in this lesson.

---

## 3. Interactive map-based segmentation

```{code-cell} python
image_path = download_file("https://data.source.coop/opengeos/geoai/uc-berkeley.tif")

sam3 = SamGeo3(backend="transformers", device=None, checkpoint_path=None, load_from_HF=True)
sam3.set_image(image_path)
sam3.generate_masks(prompt="building")
sam3.save_masks("masks.tif")

sam3.show_map(height="700px", min_size=10)
```

Note `backend="transformers"` here; the interactive interface specifically requires this backend, unlike the `"meta"` backend used in earlier pages. `show_map()` supports two interaction modes directly in the notebook: typing a text description (like "tree" or "parking lot") and clicking Segment, or drawing a rectangle over an area of interest and clicking Segment. Results update without re-running the image encoder, the same cached-embedding efficiency from earlier in this lesson, now available through a point-and-click interface rather than code.

```{tip}
Interactive exploration with `show_map()` is a good first step even for a project you plan to eventually code as a script: it lets you quickly test whether your target feature is reliably segmentable before investing in a full coded workflow.
```

---

## 4. Video segmentation

```{admonition} This section extends the lesson beyond the core image workflow
:class: note
Video segmentation is a genuine extension of everything covered so far, useful for monitoring and tracking applications, but it is not required to apply the rest of this lesson to a typical single-image geospatial project. Treat this section as optional depth.
```

### A. Text-prompted video segmentation

```{code-cell} python
video_path = download_file("https://data.source.coop/opengeos/geoai/cars.mp4")

sam = SamGeo3Video()
sam.set_video(video_path)
sam.generate_masks("car")
sam.show_frames(frame_stride=20, ncols=3)
```

A text prompt on video finds all matching objects in the first frame, then propagates their masks through every remaining frame automatically, using the {term}`streaming memory <Streaming Memory>` mechanism from earlier in this lesson to maintain object identity as cars move.

### B. Point-prompted video segmentation

```{code-cell} python
sam = SamGeo3Video()
sam.set_video(video_path)
sam.init_tracker()
sam.show_frame(0, axis="on")

sam.add_point_prompts([[300, 200]], [1], obj_id=1, frame_idx=0)
sam.add_point_prompts([[420, 200]], [1], obj_id=2, frame_idx=0)
sam.propagate()
```

Each `add_point_prompts()` call assigns an {term}`instance identifier <Instance Identifier>` (`obj_id`) that persists across the entire video, the same "same ID means same object" idea from L08, now tracked through time rather than assigned once per static image. `propagate()` is the step that actually runs tracking across all frames using the accumulated prompts; nothing propagates until you call it.

Negative (background) points refine an individual object's mask, the same idea from the point-prompt page, applied here to a specific tracked object:

```{code-cell} python
sam.add_point_prompts(
    points=[[335, 195], [335, 220]],
    labels=[1, 0],
    obj_id=1,
    frame_idx=0,
)
sam.propagate()
```

### C. Removing spurious detections

```{code-cell} python
sam.remove_object(obj_id=[5, 8, 12, 13])
sam.propagate()
```

Text-prompted video segmentation can pick up spurious detections, an object that is not actually what you meant to track. Removing them by ID *before* the final propagation avoids wasting tracking effort on objects you do not need, more efficient than tracking everything and filtering afterward.

```{code-cell} python
import os

os.makedirs("output", exist_ok=True)
sam.save_masks("output/masks")
sam.save_video("output/segmented.mp4", fps=25)
sam.close()
```

`close()` releases the video session's resources; call it when you are done with a given video, the video-tracking equivalent of `geoai.empty_cache()` used elsewhere in this lesson to free GPU memory between operations.

---

## 5. Python reactivation

`{i: f"Player {i}" for i in range(15)}`-style dictionary construction (or the equivalent explicit loop) maps object IDs to display names, the same key-value pattern used for the `stats` and `models` dictionaries in earlier lessons, here applied to labeling tracked objects for display. `remove_object(obj_id=[5, 8, 12, 13])` takes a list of IDs to remove at once, the same batch-removal idea as filtering multiple rows from a DataFrame in one operation rather than one at a time.

---

## 6. Common pitfalls

- **Using the `"meta"` backend when you need the interactive map interface.** `show_map()` specifically requires `"transformers"`; check this before starting an interactive session.
- **Forgetting to call `propagate()` after adding point prompts.** Prompts alone do not track anything; `propagate()` is the step that actually runs tracking across the video.
- **Propagating before removing spurious detections.** Cleaning up unwanted object IDs first, then propagating, is more efficient than tracking everything and discarding results afterward.
- **Not closing video sessions.** `close()` (and `shutdown()` for the predictor itself) release GPU memory; skipping this can leave resources tied up unnecessarily across a long working session.

---

## 7. Mini task

You run text-prompted video segmentation with the prompt "player" on a sports video and notice object IDs 5, 8, 12, and 13 are clearly not players (perhaps a scoreboard graphic and stray background objects). What is the most efficient order of operations from here: propagate first and clean up after, or clean up first and then propagate?

:::{dropdown} Sample solution
:class: note

Clean up first, then propagate. Removing the spurious object IDs before calling `propagate()` means tracking effort is only spent on the objects you actually want followed through the full video, rather than tracking every detection, including the unwanted ones, and discarding that wasted work afterward. This is exactly the order demonstrated in step C.
:::

---

## 8. Key takeaways

- `show_map()` provides a no-code, interactive segmentation interface, but specifically requires the `"transformers"` backend.
- `SamGeo3Video` extends prompting and streaming memory across video frames, with `propagate()` as the explicit step that runs tracking.
- Object IDs assigned through point prompts persist across the video, the same instance-identity idea from instance segmentation, now applied over time.
- Removing spurious detections before propagating is more efficient than filtering after tracking every detected object.

### Further reading

- [Interactive Segmentation of Remote Sensing Imagery with Meta's SAM 3](https://youtu.be/CIgVCOG8VJ0) — a video walkthrough of the `show_map()` interface covered above.
- [Automated Segmentation with Image Captioning + Meta's SAM 3](https://youtu.be/m8CSNgc6Ibs) — an extension combining text-prompted segmentation with automated captioning.
