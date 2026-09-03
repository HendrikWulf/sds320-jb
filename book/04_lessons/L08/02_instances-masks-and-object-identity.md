---
site:
  outline_maxdepth: 2
---

# Instances & identity

<!-- markdownlint-disable MD033 MD055 MD056 MD012-->
<div class="page-subtitle">
Understand how one semantic class becomes many individually identifiable objects
</div>

---

## 1. Motivation

Every function you use later in this lesson, training, inference, evaluation, works on the assumption that "field number 7" is meaningfully different from "field number 12," even though both are simply "field" as a class. Understanding this distinction now makes the rest of the lesson, especially reading raw prediction output, far less confusing.

---

## 2. Core idea

A semantic segmentation label answers "what class is this pixel," using one value per class. An instance segmentation label answers "what class, *and which specific object*," using one value per object. Two adjacent fields of the same crop still get two different values, because the label encodes object identity, not just class membership.

Take the field-parcel example directly: a semantic segmentation model applied to a farming region might produce one large connected region labeled "field." From that output alone, you cannot tell whether it contains 5 parcels or 50, and you cannot compute the area of any individual one. An instance segmentation model instead produces a separate mask per parcel, each with its own {term}`instance identifier <Instance Identifier>` and {term}`confidence score <Confidence Score>`, which is what makes counting, per-parcel measurement, and tracking possible.

---

## 3. How instance identity is represented

### A. Class labels and instance IDs

Consider three neighboring fields.

A semantic class raster might contain:

```text
0 0 0 0 0 0
0 1 1 1 1 0
0 1 1 1 1 0
0 0 0 0 0 0
```

where:

```text
0 = background
1 = field
```

This tells us which pixels are fields, but not how many fields exist.

An instance raster could instead contain:

```text
0 0 0 0 0 0 0
0 1 1 2 2 3 0
0 1 1 2 2 3 0
0 1 1 2 2 3 0
0 0 0 0 0 0 0
```

Now:

```text
0 = background
1 = field instance 1
2 = field instance 2
3 = field instance 3
```

The values `1`, `2` and `3` are **not different land-cover classes**. They are unique object identifiers.

This distinction is central to instance-segmentation training data.

---

### B. What one prediction contains

A typical predicted instance contains several related outputs:

```text
instance
├── class label
├── confidence score
├── bounding box
└── pixel-level mask
```

 Component | Format / Example | Role in Downstream Analytics |
| --- | --- | --- |
| *Class Label* | Category ID / Name<br>`field = 1` | Assigns the semantic category to the detected object. |
| *Instance ID* | Nominal Integer<br>`ID: 27` | Distinguishes individual entities without imposing rank (e.g., ID `27` is not "greater than" ID `3`). |
| *Confidence Score* | Probability Float<br>`0.94` (`94%`) | Quantifies model certainty; acts as a filter threshold to discard weak candidate predictions. |
| *Bounding Box* | Coordinates<br>`[xmin, ymin, xmax, ymax]` | Defines the coarse bounding envelope enclosing the object. |
| *Pixel Mask* | Binary Matrix<br>`1` = object, `0` = background | Provides exact feature geometry for area, perimeter, and shape calculations. |

---

### C. One class, many masks

The agricultural example illustrates an important point.

Suppose an image contains 35 fields.

You do **not** need:

```text
35 semantic classes
```

You need:

```text
1 semantic class: field
35 separate instances
```

This is why the practical training workflow later uses:

```python
num_classes=2
```

for:

```text
0 = background
1 = field
```

even though an image may contain many individual parcels.

Object identity comes from the masks, not from adding more semantic classes.

---

### D. Why object identity matters

Once each object remains separate, you can perform object-level operations.

For example:

```text
instance mask
     ↓
polygon
     ↓
one row in a GeoDataFrame
     ↓
area / perimeter / shape / attributes
```

This creates a familiar spatial data model:

| instance_id | class | confidence | geometry |
| ---: | --- | ---: | --- |
| 1 | field | 0.94 | Polygon |
| 2 | field | 0.88 | Polygon |
| 3 | field | 0.81 | Polygon |

The prediction has become a set of spatial features rather than only a raster class map.

---

### E. Common instance errors

Instance segmentation introduces several error types beyond incorrect class labels.

| Error Type | Statistical Class | Reference vs. Prediction | Practical GeoAI Impact |
| --- | --- | --- | --- |
| *Missed Instance* | False Negative | `Ref: 1 2 3` <br> `Pred: 1 2 -` | Undercounts objects (e.g., missing isolated tree crowns). |
| *Spurious Instance* | False Positive | `Ref: 1 - -` <br> `Pred: 1 2 -` | Overcounts objects by detecting ghosts or background noise. |
| *Merged Instances* | Structural Error | `Ref: 1 2` <br> `Pred: 1 1 1` | Combines adjacent entities (e.g., merging two farm parcels). |
| *Split Instance* | Structural Error | `Ref: 1 1 1` <br> `Pred: 1 2 3` | Artificially inflates object counts (e.g., splitting one building into three). |
| *Poor Boundary* | Geometric Error | `Ref: [  1  ]` <br> `Pred: [ 1 1 1 1 ]` | Skews area/perimeter metrics while keeping counts correct. |

```{important}
A project that uses object counts and one that uses object areas can be affected differently by the same prediction. Evaluate the type of error that matters for your downstream analysis.
```

---

### F. Mask overlap

{term}`Intersection over Union (IoU)` evaluates the geometric overlap between a predicted mask ($A$) and a reference mask ($B$):

$$\text{IoU}(A, B) = \frac{\vert{}A \cap B\vert{}}{\vert{}A \cup B\vert{}} = \frac{\text{TP}}{\text{TP} + \text{FP} + \text{FN}} = \frac{\text{Area of Overlap}}{\text{Area of Union}}$$

For instance segmentation, the overlap is calculated using the **mask pixels**, rather than only the rectangular bounding boxes.

Higher mask IoU indicates stronger spatial agreement.

This becomes useful when calculating instance-level metrics such as {term}`Average Precision (AP)`.
<!-- markdownlint-enable MD033 MD055 MD056 MD012 -->
---

## 4. Python reactivation

An instance-label raster loaded as a NumPy array behaves exactly like the class-label arrays from L07: `numpy.unique(array)` lists the distinct values present, only now those values represent individual object IDs rather than class indices. Subtracting one for the background value—`len(numpy.unique(array)) - 1`—is a quick way to count how many distinct objects a mask contains.

Suppose `instance_mask` is an array where zero is background and positive integers are individual object IDs:

```{code-cell} python
import numpy as np

instance_mask = np.array(
    [
        [0, 0, 0, 0, 0],
        [0, 1, 1, 2, 0],
        [0, 1, 1, 2, 0],
        [0, 3, 3, 2, 0],
        [0, 0, 0, 0, 0],
    ]
)

np.unique(instance_mask)

```

You can filter for non-background IDs to get exact object numbers and identifiers:

```{code-cell} python
instance_ids = np.unique(instance_mask)
instance_ids = instance_ids[instance_ids != 0]

print(f"Number of instances: {len(instance_ids)}")
print(f"IDs: {instance_ids}")

```

Or calculate the total count directly using the shortcut formula:

```{code-cell} python
num_objects = len(np.unique(instance_mask)) - 1
print(f"Quick object count: {num_objects}")

```

While this is not how a neural network generates predictions, it demonstrates why an instance-labelled raster is useful: every object retains a distinct identity and can be selected independently.

---

## 5. Common pitfalls

- **Assuming instance IDs are comparable across images.** Field 3 in one prediction has no relationship to field 3 in a different image; IDs are only meaningful within a single prediction.
- **Confusing the class-label and instance-label rasters.** They answer different questions and are easy to mix up if you are not deliberate about which one you are looking at.
- **Treating a low per-instance confidence score as a class-level problem.** A low score on one field does not mean the model is generally uncertain about fields; check whether it is isolated to that specific object.

---

## 6. Mini task

You load an instance-label raster and find `numpy.unique(array)` returns `[0, 1, 2, 3, 4, 5]`. A colleague says "so the model found 6 fields." Are they right? Explain.

:::{dropdown} Sample solution
:class: note

Not quite. Assuming 0 is reserved for background, as is the convention in this lesson, the array actually contains 5 distinct field instances (values 1 through 5), not 6. It is a common off-by-one mistake to count the background value as an object; always confirm which value represents background before counting instances.
:::

---

## 7. Key takeaways

- Class labels answer "what class"; instance labels answer "which specific object," using a distinct integer ID per object.
- `geoai` keeps class-label and instance-label rasters separate, matching the `"class_label"` and `"instance"` entries you will see in prediction output.
- Instance identifiers are only meaningful within a single prediction; they do not carry meaning across different images.
- Confidence scores in instance segmentation apply per object, the same principle as per-box confidence in detection.
