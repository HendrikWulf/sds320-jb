---
site:
  outline_maxdepth: 2
---

# Cleaning & vectorisation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Cleaning raw masks, vectorising them and computing object properties
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

The previous page produced raw instance predictions. This page cleans them up, converts them to vector polygons, and computes the geometric properties that make the result genuinely useful for downstream analysis, the same finishing sequence from L07's building workflow, adapted here for instance data specifically.

A raw prediction is rarely the finished product. Small spurious detections, tiny gaps between adjacent fields, and complex pixel-level edges all need addressing before a result is ready to share, join with other data, or analyze statistically. This page is what turns "a model produced some output" into "a usable field-boundary dataset."

---

## 2. Core idea

The finishing sequence has four steps: clean the raw instance mask, convert it to vector polygons, visually compare it against the source imagery, and compute geometric properties for each detected field. Each step depends on the one before it, and skipping the cleaning step in particular tends to show up later as noisy, hard-to-interpret statistics.

---

## 3. Workflow

Converting raw neural network predictions into GIS-ready vector data requires post-processing. While raw instance rasters assign unique IDs to detected objects, they often contain small noise artifacts, internal gaps, or boundary irregularities. This workflow covers how to clean instance rasters, convert them into vector geometries, verify spatial alignment, calculate geometric shape attributes, and use geometric properties for spatial QA/QC.

---

### A. Raw prediction recovery

First, we re-establish the dataset environment, select a test image, and generate raw instance predictions:

```{code-cell} python
from pathlib import Path
import geoai

# Download and prepare dataset pathways
geoai.download_ftw(countries=["luxembourg"], output_dir="ftw_data")
data = geoai.prepare_ftw("ftw_data", country="luxembourg")

# Select test image deterministically
test_images = sorted(Path(data["test_dir"]).glob("*.tif"))
test_image_path = str(test_images[0])

# Run instance segmentation inference
model_path = "field_boundaries/models/best_model.pth"
masks_path = "field_boundary_prediction.tif"

result = geoai.instance_segmentation(
    input_path=test_image_path,
    output_path=masks_path,
    model_path=model_path,
    num_classes=2,
    num_channels=4,
    window_size=256,
    overlap=128,
    confidence_threshold=0.5,
    batch_size=4,
    vectorize=True,
    class_names=["background", "field"],
)

```

---

### B. Cleaning instance masks

Raw predictions frequently contain minor false-positive pixels (spurious noise) or internal holes (unconfident pixels inside an otherwise solid object). The `clean_instance_mask()` function addresses both issues:

```{code-cell} python
cleaned_masks_path = "field_boundary_prediction_cleaned.tif"

geoai.clean_instance_mask(
    result["instance"],
    cleaned_masks_path,
    min_area=100,
    max_hole_area=100,
)

```

Post-processing operates through two spatial parameters:

1. **`min_area=100` (Removing Spurious Noise):** Connected pixel components smaller than $100$ cells are removed.

* *Resolution Context:* For Sentinel-2 imagery with $10\text{ m}$ pixels, one pixel equals $100\text{ m}^2$. A $100$-pixel threshold removes objects smaller than $10,000\text{ m}^2$ ($1\text{ hectare}$).
* *Methodological Trade-Off:* Setting `min_area` too low retains spurious false-positive noise, while setting it too high accidentally deletes real, small agricultural fields.

1. **`max_hole_area=100` (Filling Internal Gaps):** Internal gaps or unassigned pixels within a detected field up to $100$ pixels are filled, producing solid object geometries.

```{admonition} Use clean_instance_mask, not clean_raster
:class: important
`clean_raster()` (used in semantic segmentation) operates on category values. Applying it to instance masks will corrupt object boundaries by treating distinct instance IDs as categorical classes. `clean_instance_mask()` is specifically designed to preserve independent object identities ($1, 2, 3 \dots$) while cleaning shapes.

```

---

### C. Vectorizing the cleaned mask

Once the instance raster is cleaned, `raster_to_vector()` converts each distinct object ID into an individual vector polygon feature:

```{code-cell} python
output_vector_path = "field_boundary_prediction.geojson"
gdf = geoai.raster_to_vector(cleaned_masks_path, output_vector_path)

gdf.head()

```

{term}`Vectorization` converts each distinct raster instance into a row in a GeoPandas `GeoDataFrame`. Because spatial georeferencing is transferred directly from the source GeoTIFF, these polygons can immediately be exported to GeoJSON, GeoPackage, or Shapefile formats for use in desktop GIS software.

---

### D. Compare predictions with imagery

Before calculating statistics on vector outputs, visually inspect polygon boundaries against the original imagery using a {term}`Split-Panel Map`:

```{code-cell} python
geoai.create_split_map(
    left_layer=gdf,
    right_layer=test_image_path,
    left_args={"style": {"color": "red", "fillOpacity": 0.2}},
    basemap=test_image_path,
)

```

Interactive split maps allow you to swipe between vector polygons and raw imagery to perform visual QA/QC checks:

* Are neighboring fields correctly separated into distinct polygons?
* Did any valid small fields get deleted by the `min_area` filter?
* Do polygon boundaries follow visible crop edges, or do they cut across natural boundaries?

---

### E. Coordinate Reference System

Calculating real-world dimensions (e.g., area in hectares or perimeter in meters) requires verifying your dataset's {term}`Coordinate Reference System (CRS)`.

```{important}
If your GeoDataFrame uses a geographic CRS (latitude/longitude in degrees, such as EPSG:4326), area and perimeter calculations will yield meaningless degree-based units. Always reproject your vectors to an appropriate projected CRS (such as local UTM zones) before computing geometric attributes.

```

$$\text{Geographic CRS (Degrees)} \xrightarrow{\quad \text{Reproject} \quad} \text{Projected CRS (Meters) } \longrightarrow \text{Valid Area (\text{ha}) \& Perimeter (\text{m})}$$

---

### F. Compute geometric properties

With an appropriate projected CRS, `add_geometric_properties()` computes quantitative shape attributes for every detected polygon:

```{code-cell} python
gdf_props = geoai.add_geometric_properties(gdf, area_unit="ha", length_unit="m")
gdf_props.describe()

```

| Property | Formula / Ratio | Spatial & Analytical Interpretation |
| --- | --- | --- |
| **Area** | $\text{Area in ha}$ | Total surface area; crucial for agricultural yield forecasting and land management. |
| **Perimeter** | $\text{Boundary Length in m}$ | Outer boundary length; useful for fencing, edge-effect studies, and access planning. |
| **Elongation** | $\frac{\text{Major Axis}}{\text{Minor Axis}}$ | Quantifies how stretched a shape is. Differentiates compact, square parcels ($1.0$) from long strip fields ($>3.0$). |
| **Solidity** | $\frac{\text{Area}}{\text{Convex Hull Area}}$ | Measures boundary smoothness and convexity ($1.0$ = completely convex, $<0.7$ = highly irregular or indented boundaries). |
| **Extent** | $\frac{\text{Area}}{\text{Bounding Box Area}}$ | Indicates how fully a polygon fills its minimum bounding rectangle. |

---

### G. Interpreting geometric attributes

Visualizing spatial patterns based on geometric attributes turns model outputs into actionable spatial insights:

```{code-cell} python
# Visualize spatial distribution of field sizes
geoai.view_vector_interactive(gdf_props, column="area_ha", tiles=test_image_path)

# Visualize field shape elongation
geoai.view_vector_interactive(gdf_props, column="elongation", tiles=test_image_path)

```

Coloring fields by `area_ha` or `elongation` exposes geographical structure across landscapes—such as large, regular parcels in flat agricultural valleys versus small, highly elongated terraced fields on hillslopes.

---

### H. Geometry-based error diagnostics

Geometric attributes also serve as automated diagnostic tools for detecting model errors across large regional datasets:

```text
              Quantitative Geometric Diagnostics
              
   [ Extremely Small Area ]           [ High Elongation / Low Solidity ]
   (< 0.05 ha / < 5 pixels)               (Sliver Polygon Artifacts)
               │                                      │
               ▼                                      ▼
      Fragmented Mask Error                 Boundary Splitting Error
   
   [ Extremely Large Area ]              [ Low Solidity / Indented ]
        (> 100 ha)                        (Over-merged Fields)
               │                                      │
               ▼                                      ▼
      Merged Fields Error                   Complex Boundary Artifact

```

* **Fragmented Mask Detection:** A high count of polygons smaller than $0.05\text{ ha}$ indicates mask fragmentation or overly permissive confidence thresholds.
* **Merged Field Detection:** Unusually large area outliers ($>50\text{ ha}$) often reveal places where the model failed to detect a boundary, merging neighboring parcels into a single polygon.
* **Sliver Polygons:** Polygons with high elongation ($>5.0$) and low solidity often represent narrow tile-edge artifacts rather than real fields.

:::note Domain-Specific Post-Processing: Fields vs. Buildings
:class: dropdown

Post-processing rules must adapt to the physical geometry of your target object:

* **Agricultural Fields:** Natural land parcels exhibit organic curves and irregular boundaries. Smoothing algorithms should preserve natural boundary variations without forcing right angles.
* **Building Footprints:** Man-made structures are predominantly rectilinear. Post-processing workflows for buildings typically apply **orthogonalization** algorithms to snap irregular pixel edges into $90^\circ$ right angles and straight walls.

*Never apply building orthogonalization filters to natural features like fields, water bodies, or forest patches.*
:::

---

## 4. Common pitfalls

* **Using `clean_raster()` on instance output.** As emphasized above, this is built for class masks, not per-object instance masks; use `clean_instance_mask()` instead.
* **Setting `max_hole_area` too high.** An overly generous hole-filling threshold can bridge a genuine gap between two adjacent, distinct fields, silently merging what should be two separate instances.
* **Computing area and perimeter in an unprojected CRS.** Latitude-longitude coordinates do not represent true ground distances; reproject first.
* **Filtering by `min_area` without checking what gets discarded.** A small real field and a small spurious detection can look identical by area alone; spot-check a few filtered-out polygons before trusting the threshold.

---

## 5. Mini task

After cleaning and vectorizing, your field dataset shows a small cluster of polygons with unusually high elongation values (around 8 to 10) in one corner of the study area, while the rest of the fields have elongation values under 3. What are two possible explanations, and how would you investigate which one applies?

:::{dropdown} Sample solution
:class: note

One possibility is that these are genuinely elongated, strip-shaped fields, which do occur in some landscapes and farming traditions. Another possibility is a systematic prediction error, for example several adjacent fields being merged into one long, thin detection, or a single field being incorrectly split along its length. To investigate, you would visually compare that specific cluster against the source imagery using the split map from step C, checking whether the field boundaries visible in the imagery genuinely match the elongated shapes, or whether they suggest a merging or splitting error in the prediction.
:::

---

## 6. Key takeaways

* `clean_instance_mask()`, not `clean_raster()`, is the correct tool for cleaning instance segmentation output, since it operates on per-object pixel values rather than class categories.
* Vectorizing a cleaned instance mask produces one polygon per detected object, ready for GIS use.
* Geometric properties (area, perimeter, elongation, solidity, extent) turn raw polygons into an analyzable attribute table, but are only meaningful in a projected CRS.
* Visual comparison against source imagery remains necessary even after cleaning; unusual property values are often a signal worth investigating rather than trusting or discarding automatically.
