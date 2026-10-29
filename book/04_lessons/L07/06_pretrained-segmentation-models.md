---
site:
  outline_maxdepth: 2
---

# Pretrained models

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Use existing segmentation models for rapid inference
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

In the previous chapter, you prepared labelled imagery and trained U-Net models to detect water. That approach gives you control over the training data and model, but it also requires labels, computational resources, and time. What if you need a **first water map today**, or you want to identify clouds before analysing an image?

A {term}`pre-trained model <Pre-Trained Model>` lets you apply an existing model to new imagery. In this chapter, you will run **two applied studies** using three small, georeferenced GeoTIFFs:

1. **OmniWaterMask:** delineate water bodies in six-band Sentinel-2 imagery and four-band VEXCEL aerial imagery.
2. **OmniCloudMask:** identify clear pixels, thick clouds, thin clouds, and cloud shadows in another Sentinel-2 image.

Each study follows a short, visible workflow: **inspect imagery → run inference → compare the result with the image → use or save the output**. No custom training labels are required.

```{admonition} A model prediction is not ground truth
:class: important
A model can run successfully and still produce an incorrect map. Pay particular attention to dark surfaces and shadows in water mapping, and bright terrain, thin clouds, and cloud-shadow boundaries in cloud mapping. The visual checks below are part of the workflow, not optional decoration.
```

---

## 2. Core idea

Both models perform pixel-level {term}`semantic segmentation <Semantic Segmentation>`, but they answer different questions. OmniWaterMask identifies **water versus non-water**; OmniCloudMask assigns **four cloud-related classes**. Neither requires you to train a new network.

A {term}`sensor-agnostic model <Sensor-Agnostic Model>` can work with several sensors, provided the imagery supplies the correct spectral channels and has suitable data characteristics. **Sensor-agnostic does not mean band-order-agnostic.** We must tell each function which raster bands correspond to the channels it expects.

| Input image | Raster band order in this lesson | Channels used by the model | Configuration |
| --- | --- | --- | --- |
| Sentinel-2 water | Blue, green, red, NIR, SWIR1, SWIR2 | Red, green, blue, NIR | `band_order="sentinel2"` → `[3, 2, 1, 4]` |
| VEXCEL water | Blue, green, red, NIR | Red, green, blue, NIR | `band_order=[3, 2, 1, 4]` |
| Sentinel-2 clouds | Blue, green, red, NIR, SWIR1, SWIR2 | Red, green, NIR | `red_band=3, green_band=2, nir_band=4` |

All indices in this table are **1-based**, as in Rasterio. GeoAI's water function selects and reorders the four channels internally; you do **not** need to rewrite either GeoTIFF. Both Sentinel-2 and the course VEXCEL imagery store blue, green, red, and NIR as bands 1–4, so both use the mapping `[3, 2, 1, 4]` for OmniWaterMask. The Sentinel-2 SWIR bands remain in the file but are not among OmniWaterMask's four mapped inputs.

```{admonition} Same six-band Sentinel-2 export, two different models
:class: note
The earlier *supervised* six-channel U-Net was trained using all six bands in a fixed order. OmniWaterMask instead selects four channels from the six-band raster. OmniCloudMask uses just three. The original GeoTIFF stays unchanged; only the function arguments differ.
```

---

## 3. Preprocessing

### A. Set up the environment

Use the GeoAI environment introduced earlier in L07. The optional dependencies for the pretrained models are included in `geoai-py[extra]`; the first inference may download model weights and can require a network connection. A GPU is useful, but these small scenes are intended to be approachable for independent exploration.

```{code-cell} python
# If required in a new environment, run once and restart the kernel:
# %pip install -U "geoai-py[extra]"

from pathlib import Path
import numpy as np
import rasterio
import matplotlib.pyplot as plt
from matplotlib.colors import ListedColormap, BoundaryNorm
from matplotlib.patches import Patch
import geoai

WORK_DIR = Path("sds320_pretrained")
DATA_DIR = WORK_DIR / "data"
WATER_DIR = WORK_DIR / "water"
CLOUD_DIR = WORK_DIR / "clouds"

for folder in [DATA_DIR, WATER_DIR, CLOUD_DIR]:
    folder.mkdir(parents=True, exist_ok=True)

BASE_URL = "https://data.source.coop/giuz/sds320/L07/data/"
```

### B. Download the imagery

The datasets are already cropped to manageable study areas. We keep the water and cloud Sentinel-2 scenes separate: they serve different purposes and should not accidentally overwrite one another.

```{code-cell} python
files = {
    "water_s2": "S2B_L2A_20240718_N0510_R127_T14VMQ_subset.tif",
    "water_vexcel": "vexcel_water_test_subset.tif",
    "cloud_s2": "S2B_L2A_20250427_N0511_R025_T19QBA_subset.tif",
}

local_files = {}
for name, filename in files.items():
    path = DATA_DIR / filename
    if not path.exists():
        geoai.download_file(BASE_URL + filename, output_path=str(path))
    local_files[name] = path
    print(f"{name:14s} → {path}")

water_s2_path = local_files["water_s2"]
water_vexcel_path = local_files["water_vexcel"]
cloud_s2_path = local_files["cloud_s2"]
```

### C. Inspect metadata 

Check the band count, projection, pixel size, and data type. These details matter more than the file extension: a correct-looking GeoTIFF can still have its bands in an unexpected order.

```{code-cell} python
for name, path in local_files.items():
    with rasterio.open(path) as src:
        print(f"\n{name}: {path.name}")
        print(f"  size: {src.width} × {src.height} pixels")
        print(f"  bands: {src.count}; types: {src.dtypes}")
        print(f"  CRS: {src.crs}; pixel size: {src.res}")
        print(f"  NoData: {src.nodata}; descriptions: {src.descriptions}")

with rasterio.open(water_s2_path) as src:
    assert src.count == 6, "Expected six Sentinel-2 bands."
with rasterio.open(cloud_s2_path) as src:
    assert src.count == 6, "Expected six Sentinel-2 bands."
with rasterio.open(water_vexcel_path) as src:
    assert src.count == 4, "Expected four VEXCEL bands."
```

For the two Sentinel-2 exports, the six-band sequence is **B2, B3, B4, B8, B11, B12** (blue, green, red, near-infrared, SWIR1, SWIR2), as specified for the supplied course dataset. The VEXCEL download script (`vexcel_download.py`) writes its four-band orthoimage in the order **blue, green, red, NIR** and stores these names in the raster-band descriptions. Both sources therefore use bands 3, 2, 1, and 4 when a model requests red, green, blue, and NIR. Checking the raster metadata still helps you recognize unexpected inputs in your own project.

### D. Reusable image-preview function

We will use one small Python function to display true-colour or false-colour composites. The stretch is calculated **for display only**: it does not modify the input data used by the models.

```{code-cell} python
def preview_rgb(path, indexes):
    """Read three 1-based raster bands and stretch each for display."""
    with rasterio.open(path) as src:
        data = src.read(indexes).astype("float32")
        valid = src.dataset_mask() > 0

    rgb = np.zeros_like(data, dtype="float32")
    for i, band in enumerate(data):
        good = valid & np.isfinite(band)
        if not good.any():
            continue
        low, high = np.percentile(band[good], [2, 98])
        rgb[i] = np.clip((band - low) / max(high - low, 1e-6), 0, 1)

    return np.moveaxis(rgb, 0, -1)

fig, axes = plt.subplots(1, 3, figsize=(16, 5))
for ax, (path, indexes, title) in zip(axes, [
    (water_s2_path, [3, 2, 1], "Sentinel-2: water study (RGB)"),
    (water_vexcel_path, [3, 2, 1], "VEXCEL: water study (RGB)"),
    (cloud_s2_path, [3, 2, 1], "Sentinel-2: cloud study (RGB)"),
]):
    ax.imshow(preview_rgb(path, indexes))
    ax.set_title(title)
    ax.axis("off")
plt.tight_layout()
plt.show()
```

**What do you notice?** In the water images, identify the largest visible water bodies. In the cloud image, look for bright cloud tops and potential darker cloud shadows. You will compare these observations with the model predictions.

---

## 4. OmniWaterMask on Sentinel-2

### A. Inspect a false-colour composite

{term}`OmniWaterMask` combines a pretrained water-segmentation model with spectral and contextual information. Its Sentinel-2 preset takes **red, green, blue, and NIR** from our six-band GeoTIFF. Since water generally absorbs strongly in NIR, a NIR–red–green false-colour composite can make lakes and rivers easier to inspect visually.

```{code-cell} python
fig, ax = plt.subplots(figsize=(9, 6))
ax.imshow(preview_rgb(water_s2_path, [4, 3, 2]))
ax.set_title("Sentinel-2 | NIR–Red–Green")
ax.axis("off")
plt.show()
```

### B. Predict water and vectorize it

One `geoai.segment_water()` call can save the **binary water-mask GeoTIFF**, produce **water polygons**, and optionally smooth their boundaries. We use a modest minimum polygon size to remove tiny isolated predictions. In this API, `min_size` means **number of raster pixels**, not square metres.

```{code-cell} python
s2_water_mask_path = WATER_DIR / "sentinel2_omniwater_mask.tif"
s2_water_vector_path = WATER_DIR / "sentinel2_omniwater_polygons.geojson"

s2_water_gdf = geoai.segment_water(
    input_path=str(water_s2_path),
    band_order="sentinel2",  # B4, B3, B2, B8 → R, G, B, NIR
    output_raster=str(s2_water_mask_path),
    output_vector=str(s2_water_vector_path),
    batch_size=2,
    min_size=10,            # minimum object size in pixels
    smooth=True,
    smooth_iterations=2,
)

print(f"Detected water polygons: {len(s2_water_gdf)}")
print(f"Mask: {s2_water_mask_path}")
print(f"Polygons: {s2_water_vector_path}")
```

The output is a **prediction**, not an independently validated water inventory. OmniWaterMask can also use OpenStreetMap contextual layers; its default settings may require downloading those data and can influence the result. Document these settings if you compare model versions or study areas.

### C. Visualize the outcome

Here is a reusable visual comparison: **original image → binary mask → water overlay**. The white regions in the middle panel are predicted water; blue overlays highlight the same pixels on the imagery.

```{code-cell} python
def show_water_result(image_path, mask_path, indexes, label):
    image = preview_rgb(image_path, indexes)
    with rasterio.open(image_path) as src:
        image_transform, image_crs = src.transform, src.crs
    with rasterio.open(mask_path) as src:
        mask = src.read(1)
        assert src.transform == image_transform and src.crs == image_crs

    assert mask.shape == image.shape[:2], "Raster and mask shapes differ."
    print(f"{label} water-mask values: {np.unique(mask)}")
    water = (mask == 1)

    fig, axes = plt.subplots(1, 3, figsize=(16, 5))
    axes[0].imshow(image)
    axes[0].set_title("Original imagery")
    axes[1].imshow(water, cmap="gray", vmin=0, vmax=1)
    axes[1].set_title("Predicted water mask")
    axes[2].imshow(image)
    axes[2].imshow(np.ma.masked_where(~water, water),
                   cmap=ListedColormap(["dodgerblue"]), alpha=0.55)
    axes[2].set_title("Water overlay")
    for ax in axes:
        ax.axis("off")
    fig.suptitle(label)
    plt.tight_layout()
    plt.show()

show_water_result(
    water_s2_path, s2_water_mask_path,
    indexes=[3, 2, 1], label="Sentinel-2 | OmniWaterMask"
)
```

**Interpretation:** Which shores follow the image closely? Where could dark shadows, wetlands, or mixed shoreline pixels cause false positives or omissions?

### D. Explore the result interactively

The vector polygons retain their geographic coordinates, so you can use them directly in GIS. A split map is particularly helpful when checking whether the model follows narrow channels or small water bodies.

```{code-cell} python
if not s2_water_gdf.empty:
    split_map = geoai.create_split_map(
        left_layer=s2_water_gdf,
        right_layer=str(water_s2_path),
        left_args={"style": {"color": "blue", "fillOpacity": 0.3}},
        right_args={"indexes": [3, 2, 1], "vmin": 0, "vmax": 4000},
        basemap=str(water_s2_path),
    )
    display(split_map)
else:
    print("No water polygons to show. Inspect the raster mask first.")
```

The map provides an interpretation aid; it is not a quantitative accuracy assessment. You would need independent reference labels for metrics such as {term}`Intersection over Union (IoU) <Intersection over Union>`.

---

## 5. OmniWaterMask on VEXCEL

Now apply **the same pretrained water model** to four-band aerial imagery. This is a useful test of cross-sensor transfer: you are changing the image source and pixel size, but you are not training a different network.

### A. Confirm the band mapping

The SDS320 VEXCEL download script explicitly exports **blue, green, red, NIR** (bands 1–4). OmniWaterMask requires the channel order **red, green, blue, NIR**, so we pass the *indices* `[3, 2, 1, 4]`. This selects the correct channels without rearranging or rewriting the GeoTIFF. For visualization, RGB is `[3, 2, 1]` and a NIR–red–green composite is `[4, 3, 2]`.

```{code-cell} python
# VEXCEL raster: 1=Blue, 2=Green, 3=Red, 4=NIR.
VEXCEL_BAND_ORDER = [3, 2, 1, 4]  # Model input: Red, Green, Blue, NIR
VEXCEL_RGB = [3, 2, 1]          # Display: Red, Green, Blue
VEXCEL_FALSE_COLOUR = [4, 3, 2]  # Display: NIR, Red, Green

with rasterio.open(water_vexcel_path) as src:
    assert src.count == 4, "Expected four VEXCEL bands."
    descriptions = tuple((name or "").lower() for name in src.descriptions)
    if all(descriptions):
        assert descriptions == ("blue", "green", "red", "nir"), (
            f"Unexpected VEXCEL band descriptions: {src.descriptions}"
        )
    print("VEXCEL stored bands [B, G, R, NIR]:", src.descriptions)
    print("Mapped model channels [R, G, B, NIR]:", VEXCEL_BAND_ORDER)

fig, ax = plt.subplots(figsize=(9, 6))
ax.imshow(preview_rgb(water_vexcel_path, VEXCEL_FALSE_COLOUR))
ax.set_title("VEXCEL | NIR–Red–Green")
ax.axis("off")
plt.show()
```

### B. Run the model 

Unlike the previous chapter, there are no training tiles, validation curves, or locally trained checkpoints. The same `segment_water()` function processes the aerial image and exports a mask and polygons.

```{code-cell} python
vexcel_water_mask_path = WATER_DIR / "vexcel_omniwater_mask.tif"
vexcel_water_vector_path = WATER_DIR / "vexcel_omniwater_polygons.geojson"

vexcel_water_gdf = geoai.segment_water(
    input_path=str(water_vexcel_path),
    band_order=VEXCEL_BAND_ORDER,
    output_raster=str(vexcel_water_mask_path),
    output_vector=str(vexcel_water_vector_path),
    batch_size=2,
    min_size=10,
    smooth=True,
    smooth_iterations=2,
)

print(f"Detected water polygons: {len(vexcel_water_gdf)}")
```

### C. Visualize the prediction

Run the same three-panel visualization as above. Look particularly at **small ponds, narrow water channels, and dark non-water surfaces**. Fine-resolution imagery provides more boundary detail, but that detail can also make predictions noisier.

```{code-cell} python
show_water_result(
    water_vexcel_path, vexcel_water_mask_path,
    indexes=VEXCEL_RGB,
    label="VEXCEL | OmniWaterMask"
)
```

Display the resulting water-body boundaries over the aerial image. Drag the split-map divider to compare them with the original shoreline geometry.

```{code-cell} python
if not vexcel_water_gdf.empty:
    split_map = geoai.create_split_map(
        left_layer=vexcel_water_gdf,
        right_layer=str(water_vexcel_path),
        left_args={"style": {"color": "blue", "fillOpacity": 0.3}},
        right_args={"indexes": VEXCEL_RGB},
        basemap=str(water_vexcel_path),
    )
    display(split_map)
else:
    print("No water polygons to show. Check the band mapping and raster mask.")
```

### D. Compare the two water outputs

Both workflows produce georeferenced water masks and vector polygons, but they are based on **different images and study areas**. Comparing the number of detected water bodies, or their total area, cannot tell you which sensor or model result is more accurate.

You can nevertheless inspect the basic output inventory:

```{code-cell} python
for name, gdf, raster in [
    ("Sentinel-2", s2_water_gdf, s2_water_mask_path),
    ("VEXCEL", vexcel_water_gdf, vexcel_water_mask_path),
]:
    with rasterio.open(raster) as src:
        print(f"{name}: {len(gdf)} polygons; "
              f"{src.width} × {src.height} mask pixels; CRS {src.crs}")
```

For an operational project, check the quality of each water mask against independent labels in its **own** study area before drawing conclusions about performance.

---

## 6. OmniCloudMask on Sentinel-2

Clouds obscure surfaces and introduce misleading reflectance values. Their shadows can be mistaken for water or other dark classes. {term}`OmniCloudMask` predicts four classes from **red, green, and NIR** channels:

| Mask value | Class | Interpretation |
| ---: | --- | --- |
| 0 | Clear | No cloud or cloud shadow predicted |
| 1 | Thick cloud | Opaque cloud cover |
| 2 | Thin cloud | Semi-transparent cloud cover |
| 3 | Cloud shadow | Shadow cast by clouds |

### A. View the cloudy Sentinel-2 image

The new cloud example uses a six-band Sentinel-2 subset. Unlike the source-book example, **the channels are not ordered red–green–blue**: our input order is *blue–green–red–NIR–SWIR1–SWIR2*. Therefore, the correct cloud-model indices are `red_band=3`, `green_band=2`, `nir_band=4`.

```{code-cell} python
fig, axes = plt.subplots(1, 2, figsize=(13, 5))
axes[0].imshow(preview_rgb(cloud_s2_path, [3, 2, 1]))
axes[0].set_title("True colour | R–G–B")
axes[1].imshow(preview_rgb(cloud_s2_path, [4, 3, 2]))
axes[1].set_title("False colour | NIR–R–G")
for ax in axes:
    ax.axis("off")
plt.tight_layout()
plt.show()
```

**Look first:** Can you distinguish thick clouds, thin cloud edges, and the darker shadows next to some clouds? False colour may help separate clouds from the land surface.

### B. Predict the four-class cloud mask

Call the documented GeoAI wrapper. `inference_dtype="fp32"` is a compatible starting point on CPU and GPU. You can later try `"bf16"` on suitable hardware; choosing that format is a performance decision, not a requirement of the imagery.

```{code-cell} python
cloud_mask_path = CLOUD_DIR / "sentinel2_omnicloud_mask.tif"

geoai.predict_cloud_mask_from_raster(
    input_path=str(cloud_s2_path),
    output_path=str(cloud_mask_path),
    red_band=3,       # B4
    green_band=2,     # B3
    nir_band=4,       # B8
    batch_size=1,
    inference_dtype="fp32",
)

with rasterio.open(cloud_mask_path) as src:
    cloud_pred = src.read(1)
    cloud_profile = src.profile.copy()

with rasterio.open(cloud_s2_path) as src:
    valid = src.dataset_mask() > 0
    assert cloud_pred.shape == (src.height, src.width)

assert set(np.unique(cloud_pred[valid])).issubset({0, 1, 2, 3})
print("Cloud-mask classes:", np.unique(cloud_pred[valid]))
```

### C. Compare the image with the mask

A **discrete colour legend** makes the four prediction classes easy to interpret. The left panel displays the false-colour image; the right panel displays the predicted classes. NoData pixels are excluded from the plotted classification when the source raster marks them as invalid.

```{code-cell} python
cloud_names = ["Clear", "Thick cloud", "Thin cloud", "Cloud shadow"]
cloud_colors = ["forestgreen", "white", "silver", "#242424"]
cloud_cmap = ListedColormap(cloud_colors)
cloud_norm = BoundaryNorm(np.arange(-0.5, 4.5, 1), cloud_cmap.N)
cloud_legend = [
    Patch(facecolor=color, edgecolor="gray", label=name)
    for name, color in zip(cloud_names, cloud_colors)
]

cloud_fc = preview_rgb(cloud_s2_path, [4, 3, 2])

fig, axes = plt.subplots(1, 2, figsize=(15, 6))
axes[0].imshow(cloud_fc)
axes[0].set_title("Sentinel-2 | NIR–Red–Green")
axes[1].imshow(np.ma.masked_where(~valid, cloud_pred),
               cmap=cloud_cmap, norm=cloud_norm,
               interpolation="nearest")
axes[1].set_title("OmniCloudMask | 4 classes")
axes[1].legend(handles=cloud_legend, loc="upper right", fontsize=9)
for ax in axes:
    ax.axis("off")
plt.tight_layout()
plt.show()
```

**Interpretation:** Are thick clouds detected where the image looks bright? Where does the model draw boundaries around thin cloud edges? Can you see darker regions that were interpreted as cloud shadows?

### D. Get the cloud stats

`geoai.calculate_cloud_statistics()` reports counts and percentages for the four classes. To avoid confusing NoData pixels with class **0 (clear)**, calculate statistics only on pixels valid in the source image.

```{code-cell} python
cloud_stats = geoai.calculate_cloud_statistics(cloud_pred[valid])
for name, value in cloud_stats.items():
    if "percent" in name:
        print(f"{name}: {value:.2f}%")
    else:
        print(f"{name}: {value:,}")
```

The reported **cloud percentage** combines thick and thin cloud; the **shadow percentage** is a separate category. These values are predictions over this image subset, not the satellite provider's scene-wide cloud-cover metadata.

### E. Zoom in on a cloud boundary

Zooming is a simple way to identify segmentation errors that are invisible in a full-scene overview. The following code centres the comparison near the middle of the pixels predicted as cloud (or at the image centre if none were found).

```{code-cell} python
cloud_pixels = np.argwhere(valid & np.isin(cloud_pred, [1, 2]))
if len(cloud_pixels):
    cy, cx = np.median(cloud_pixels, axis=0).astype(int)
else:
    cy, cx = np.array(cloud_pred.shape) // 2

h, w = cloud_pred.shape
half = 200
r0, r1 = max(0, cy - half), min(h, cy + half)
c0, c1 = max(0, cx - half), min(w, cx + half)

fig, axes = plt.subplots(1, 2, figsize=(12, 5))
axes[0].imshow(cloud_fc[r0:r1, c0:c1])
axes[0].set_title("Cloud-area zoom | false colour")
axes[1].imshow(cloud_pred[r0:r1, c0:c1],
               cmap=cloud_cmap, norm=cloud_norm,
               interpolation="nearest")
axes[1].set_title("Cloud-area zoom | prediction")
for ax in axes:
    ax.axis("off")
plt.tight_layout()
plt.show()
```

### F. Create a cloud-free mask

The four-class prediction is useful for interpretation, but many downstream analyses need a simpler answer: **which pixels may be used?** `geoai.create_cloud_free_mask()` produces a binary mask in which 1 means usable and 0 means excluded.

By default, only predicted *clear* pixels are usable. Accepting thin clouds or shadows is possible but should be justified by the application.

```{code-cell} python
usable_strict = geoai.create_cloud_free_mask(cloud_pred)
usable_relaxed = geoai.create_cloud_free_mask(
    cloud_pred, include_thin_clouds=True
)

strict_pct = usable_strict[valid].mean() * 100
relaxed_pct = usable_relaxed[valid].mean() * 100
print(f"Usable (strict): {strict_pct:.1f}%")
print(f"Usable (including thin clouds): {relaxed_pct:.1f}%")

fig, axes = plt.subplots(1, 3, figsize=(16, 5))
axes[0].imshow(cloud_fc)
axes[0].set_title("Original false colour")
axes[1].imshow(np.ma.masked_where(~valid, usable_strict),
               cmap="RdYlGn", vmin=0, vmax=1)
axes[1].set_title(f"Strict usable pixels: {strict_pct:.1f}%")
axes[2].imshow(np.ma.masked_where(~valid, usable_relaxed),
               cmap="RdYlGn", vmin=0, vmax=1)
axes[2].set_title(f"Including thin clouds: {relaxed_pct:.1f}%")
for ax in axes:
    ax.axis("off")
plt.tight_layout()
plt.show()
```

Save the strict cloud-free mask as a **georeferenced** raster, preserving the original grid. We assign NoData value 255 so that invalid pixels are not confused with legitimate excluded pixels (value 0).

```{code-cell} python
usable_path = CLOUD_DIR / "sentinel2_usable_strict.tif"
usable_on_disk = np.where(valid, usable_strict, 255).astype("uint8")

profile = cloud_profile.copy()
profile.update(count=1, dtype="uint8", nodata=255, compress="lzw")

with rasterio.open(usable_path, "w", **profile) as dst:
    dst.write(usable_on_disk, 1)

print("Saved georeferenced usable-pixel mask:", usable_path)
```

### G. Turn clouds into polygons

The original four-class raster should remain your **primary analytical result**. For a map or project illustration, you may also vectorize thick and thin clouds into polygons. The following step excludes clear areas and shadows, then uses GeoAI's raster-to-vector function. Shadows remain available in the original classified mask.

```{code-cell} python
cloud_binary_path = CLOUD_DIR / "cloud_regions_binary.tif"
cloud_vectors_path = CLOUD_DIR / "cloud_regions.geojson"
cloud_binary = (valid & np.isin(cloud_pred, [1, 2])).astype("uint8")

binary_profile = cloud_profile.copy()
binary_profile.update(count=1, dtype="uint8", nodata=0, compress="lzw")
with rasterio.open(cloud_binary_path, "w", **binary_profile) as dst:
    dst.write(cloud_binary, 1)

if cloud_binary.any():
    cloud_gdf = geoai.raster_to_vector(
        raster_path=str(cloud_binary_path),
        output_path=str(cloud_vectors_path),
    )
    print(f"Mapped cloud polygons: {len(cloud_gdf)}")
else:
    cloud_gdf = None
    print("No thick or thin cloud pixels were predicted.")
```

Use an interactive split map if cloud polygons were detected. Comparing the polygons with the original image is more informative than looking at the vector boundaries alone.

```{code-cell} python
if cloud_gdf is not None and not cloud_gdf.empty:
    split_map = geoai.create_split_map(
        left_layer=cloud_gdf,
        right_layer=str(cloud_s2_path),
        left_args={"style": {"color": "cyan", "fillOpacity": 0.25}},
        right_args={"indexes": [3, 2, 1], "vmin": 0, "vmax": 4000},
        basemap=str(cloud_s2_path),
    )
    display(split_map)
else:
    print("No cloud polygons found. Inspect the predicted cloud raster.")
```

**Caution:** Vectorization and polygon simplification are presentation or analysis choices. They do not make incorrect pixel labels correct. If you later smooth polygons, retain the original raster mask for reproducibility and evaluation.

---

## 7. Checklist

The three examples demonstrate a valuable shortcut: you can generate a water or cloud mask without collecting training labels. But deployment on a new image still requires testing.

| Check | Why it matters | Where to look |
| --- | --- | --- |
| **Band mapping** | A wrong index can create plausible-looking but incorrect results | Both Sentinel-2 and VEXCEL use `[3, 2, 1, 4]` for water; Sentinel-2 clouds use red=3, green=2, NIR=4 |
| **Data scale and NoData** | Model assumptions may not match raw integer values, and excluded pixels may be counted as clear | Raster metadata, input documentation, valid-pixel mask |
| **Transfer to another sensor** | A pretrained model does not guarantee equal performance for every resolution, landscape, or season | Water boundaries in both study areas |
| **External reference information** | Contextual datasets used by OmniWaterMask may affect detections | Model parameters and processing logs |
| **Class interpretation** | Cloud shadow and thin cloud are not automatically acceptable for all applications | Four-class mask and usable-pixel settings |
| **No reference labels** | Model agreement with imagery is a useful check, but not a measured accuracy | Visual comparison versus independent validation data |

### Python reactivation

The download loop earlier uses a dictionary (`files.items()`) to avoid repeating nearly identical code. In the cloud workflow, `np.isin(cloud_pred, [1, 2])` creates a **boolean array** identifying the two cloud classes. Combining that result with `valid & ...` excludes invalid pixels. These small operations are widely useful in spatial raster analysis.

For your own SDS320 project, ask: *Is a pretrained result adequate as a first-pass map, or do I need local reference data, an independent evaluation, and possibly a custom-trained model?*

---

## 8. Mini task

**Time: approximately 10–15 minutes.** Using the cloud prediction from Study 2:

1. Calculate the proportion of valid pixels considered usable under three policies: **strict clear only**, **clear + thin cloud**, and **clear + thin cloud + shadow**.
2. Display the three masks next to one another.
3. Decide which definition you would adopt for an application that maps surface water and explain why.

Do not rerun model inference: use the predicted `cloud_pred` array already in memory.

:::{note} Sample solution
:class: dropdown

```python
policies = {
    "Strict": (False, False),
    "Include thin clouds": (True, False),
    "Include thin clouds and shadows": (True, True),
}

fig, axes = plt.subplots(1, 3, figsize=(15, 5))
for ax, (name, (thin, shadows)) in zip(axes, policies.items()):
    usable = geoai.create_cloud_free_mask(
        cloud_pred,
        include_thin_clouds=thin,
        include_shadows=shadows,
    )
    pct = usable[valid].mean() * 100
    ax.imshow(np.ma.masked_where(~valid, usable),
              cmap="RdYlGn", vmin=0, vmax=1)
    ax.set_title(f"{name}\n{pct:.1f}% usable")
    ax.axis("off")
plt.tight_layout()
plt.show()
```

For surface-water mapping, **strict clear only** is a defensible default because shadows can be mistaken for water and thin clouds can alter spectral information. A more permissive choice may preserve additional coverage but requires separate validation. The percentages depend on the actual model predictions; do not assume one policy is appropriate for every application.

:::

---

## 9. Key takeaways

- **Pretrained segmentation reduces preparation time**, but does not eliminate the need for careful inspection and validation.
- **Band order must be explicit.** Both the six-band Sentinel-2 raster and four-band VEXCEL raster use `[3, 2, 1, 4]` for OmniWaterMask's red, green, blue, and NIR inputs; OmniCloudMask uses red, green, and NIR with indices **3, 2, 4** for the course Sentinel-2 data.
- **The same water model can be applied to Sentinel-2 and VEXCEL**, provided the channels are correctly mapped and the input characteristics are compatible.
- **Cloud masks contain several meaningful classes.** Convert them into usable-pixel masks according to the requirements of your downstream task, not simply the model defaults.
- **Keep georeferenced raster outputs and document processing choices.** Vector overlays and split maps support interpretation; independent labels are needed for accuracy claims.

### Further reading

- [GeoAI: Water Segmentation with OmniWaterMask](https://opengeoai.org/examples/water_segmentation/) — complete two-sensor water workflow and map comparisons.
- [GeoAI: Water module API](https://opengeoai.org/water/) — documented `segment_water()` arguments, band-order presets, and vectorization.
- [GeoAI: Cloud and Cloud Shadow Detection](https://opengeoai.org/examples/cloud_detection/) — cloud classes, visualizations, postprocessing, and usable-pixel masks.
- [GeoAI: Cloudmask module API](https://opengeoai.org/cloudmask/) — band-index arguments and cloud-free-mask options.
- [OmniWaterMask](https://github.com/DPIRD-DMA/OmniWaterMask) and [OmniCloudMask](https://github.com/DPIRD-DMA/OmniCloudMask) — upstream implementations and model information.
