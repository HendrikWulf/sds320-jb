---
site:
  outline_maxdepth: 2
---

# When to use instances

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Recognising when you need individual object outlines, not class maps or bounding boxes
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

Instance segmentation is more expensive to label, train, and run than detection or semantic segmentation alone. Reaching for it by default, rather than because your project genuinely needs object-level shapes, costs you time and data you may not have. Getting this decision right early avoids discovering the mismatch after a training run.

---

## 2. Core idea

You now have three related tools, and each answers a different question:

- {term}`Object detection <Object Detection>` (L06) answers "where are the objects, roughly?" with a {term}`bounding box <Bounding Box>` per object.
- {term}`Semantic segmentation <Semantic Segmentation>` (L07) answers "which pixels belong to this class?" with one shared {term}`mask <Mask>` per class.
- {term}`Instance segmentation <Instance Segmentation>` answers both at once: "which pixels belong to *this specific object*?" with a separate mask per object.

Instance segmentation is worth its added cost when your project genuinely needs to count individual objects, measure each one's shape or area separately, or track a specific object over time, and when those objects have meaningful, non-trivial boundaries worth tracing precisely.

```{admonition} A quick mental test
:class: tip
Ask: "If I had a bounding box for every object, would that be enough?" If yes, stay with detection. Ask: "If I had one shared mask for the whole class, would that be enough?" If yes, stay with semantic segmentation. Only if you need both a precise boundary *and* a way to tell objects apart does instance segmentation earn its extra cost.
```

---

## 3. Points to consider

### A. Decision Matrix Table

| Analytical Goal | Recommended Method | Why Choose This Method? |
| --- | --- | --- |
| **Counting distinct entities** | Instance Segmentation | Assigns unique IDs to contiguous or overlapping features (e.g., tree crowns, buildings). |
| **Object geometry & metrics** | Instance Segmentation | Calculates individual area, perimeter, shape, or orientation directly from polygons. |
| **Boundary separation** | Instance Segmentation | Keeps adjacent entities separate (e.g., neighboring farm fields) rather than merging them. |
| **Exact boundary shape** | Instance Segmentation | Provides true visible geometry rather than loose bounding boxes (e.g., building footprints). |
| **Continuous land cover** | Semantic Segmentation | Simplies processing when individual entity boundaries do not matter (e.g., total surface water or forest cover). |
| **Presence & location** | Object Detection | Fast and sufficient when bounding boxes deliver the required count (e.g., solar panel arrays). |
| **Scene classification** | Image Recognition | Evaluates whole-tile categories without needing spatial boundaries (e.g., urban vs. forest chips). |

:::{dropdown} When instance segmentation fits
:class: note

- **Counting individual objects:** Necessary when you need discrete object totals (e.g., tree crowns, agricultural parcels). Semantic segmentation estimates total area, but cannot split connected pixels into counts.
- **Object-specific metrics:** Converts masks into distinct vector polygons to extract individual area, perimeter, shape, or orientation.
- **Preserving adjacent boundaries:** Assigns unique identities to touching features (e.g., neighboring farm fields) to prevent them from merging into a single blob.
- **Exact geometry vs. bounding boxes:** Captures true visible shape (e.g., building footprints) where a {term}`Bounding Box` introduces unnecessary background noise.
:::

:::{dropdown} When other methods are enough
:class: note

- **Semantic Segmentation for continuous cover:** Best for area-based queries (*"Which pixels are forest or water?"*) where individual patch identity is irrelevant.
- **Object Detection for location & count:** Best when approximate location and counts suffice (*"How many solar installations exist?"*). Avoid detailed masks unless geometry adds analytical value.
- **Image Recognition for scene classification:** Best for tile-level categorization (*"Is this image chip urban or agricultural?"*). Object-level boundaries are unnecessary here.
:::

---

### B. "Things" and "stuff"

A useful conceptual distinction from computer vision is between **things** and **stuff**.

**Things** are objects that can usually be counted:

- a building,
- a vehicle,
- a tree,
- a field parcel.

**Stuff** describes regions without a meaningful individual identity:

- water,
- sky,
- general vegetation,
- bare soil.

Instance segmentation mainly focuses on *things*.

:::{note} What about panoptic segmentation?
:class: dropdown

{term}`Panoptic Segmentation` combines semantic and instance segmentation.

It can represent both:

- individually identifiable **things**, and
- continuous **stuff** classes.

For example, a street scene could contain individual cars with unique IDs while every road pixel receives a shared `road` class.

SDS320 focuses on instance segmentation here because many spatial applications involve extracting one particular type of discrete object.

:::

---

### C. Resolution still matters

Accurate masks require enough spatial information to locate object boundaries.

Suppose a field boundary, narrow rooftop feature or tree crown is represented by only a few pixels. Even if the model identifies the object correctly, the exact geometry may remain uncertain.

The chain is:

```text
real-world object
        ↓
image spatial resolution
        ↓
visible boundary information
        ↓
possible mask precision
```

{term}`Spatial Resolution` therefore affects not only whether an object can be detected, but also whether its **shape can be delineated meaningfully**.

```{tip}
Zoom to the native pixel resolution of your imagery and inspect several of the smallest important objects. If you cannot confidently draw the boundary yourself, a highly precise predicted boundary is unlikely to be a defensible project assumption.
```

---

### D. Annotation effort matters

Instance segmentation requires detailed {term}`Training Data`.

Compared with scene labels or bounding boxes, pixel-level object masks generally require substantially more annotation effort.

Before choosing the method for your project, ask:

- Do suitable instance masks already exist?
- Can vector polygons be converted into masks?
- How many objects would need manual annotation?
- Can the annotation rule be applied consistently?
- Is the expected improvement over object detection worth the additional effort?

A technically suitable method may still be a poor project choice if the necessary labels cannot be produced within the available time.

---

## 4. Common pitfalls

- **Choosing instance segmentation as a default "safest" option.** It is the most expensive of the three tasks in labeling effort, training time, and compute; only choose it when your project genuinely needs the extra separation.
- **Underestimating labeling cost.** Instance-level labels take longer to produce than class-level masks or bounding boxes; factor this into your project timeline before committing.
- **Forgetting that object shape still matters for the decision.** If your objects are small and roughly uniform in shape, and only counting matters, detection may deliver most of the value at a fraction of the cost.

---

## 5. Mini task

Your project involves mapping individual tree crowns in an orchard to estimate per-tree canopy area for irrigation planning. Would detection, semantic segmentation, or instance segmentation fit best? Justify your answer in two sentences.

:::{dropdown} Sample solution
:class: note

Instance segmentation fits best. The project needs per-tree canopy *area*, which rules out detection's bounding boxes as too coarse a proxy for irregular crown shapes, and it needs *per-tree* measurements, which rules out a shared semantic segmentation mask that would not separate adjacent, touching crowns.
:::

---

## 6. Key takeaways

- Detection, semantic segmentation, and instance segmentation each answer a different question; choose based on what your project's deliverable actually requires.
- Instance segmentation is justified when you need both a precise object boundary and separation between individual objects of the same class.
- The added labeling, training, and compute cost of instance segmentation is real; do not default to it without a specific reason.
- The next page looks at how instance labels are actually structured, before the following page introduces the architecture that predicts them.

### Further reading

- IBM, ["What is Instance Segmentation?"](https://www.ibm.com/think/topics/instance-segmentation) — a general, non-geospatial introduction to the same task distinctions above.
- [Building Instance Segmentation Step-by-Step with GeoAI](https://youtu.be/Uy5HJVCky6Y) — a video walkthrough of a different instance segmentation target (buildings) using the same `geoai` workflow this lesson covers for fields.
