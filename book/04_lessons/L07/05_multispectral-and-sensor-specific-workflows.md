---
site:
  outline_maxdepth: 2
---

# Multispectral workflows

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
From four-band VEXCEL aerial imagery to six-band Sentinel-2: training and applying water-segmentation models
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

In the previous page, you used three-channel aerial imagery to segment buildings. Now we will tackle a different mapping problem: **which pixels belong to surface water?** Lakes, ponds, and rivers have complex boundaries, making them well suited to {term}`semantic segmentation <Semantic Segmentation>`.

We will train the same kind of model on two types of georeferenced imagery:

1. **VEXCEL aerial imagery:** four channels (red, green, blue, and near-infrared) for detailed water boundaries.
2. **Sentinel-2 satellite imagery:** six channels, including two shortwave-infrared bands, for water mapping with additional spectral information.

Both examples follow the full path from imagery and reference masks to **training tiles → U-Net → validation → unseen-scene prediction → water polygons**. VEXCEL comes first because it extends the familiar RGB input by one channel. Sentinel-2 then introduces six-channel imagery and batch processing of multiple scenes.

You do not need to memorize all the function arguments. Instead, look for what **stays the same** across the workflows, and what **must change** when the input sensor changes.

---

## 2. Core idea

A {term}`multispectral image <Multispectral Imagery>` records observations in several wavelength ranges. Water often reflects comparatively little energy in the {term}`near-infrared (NIR) <Near-Infrared>` and {term}`shortwave infrared (SWIR) <Shortwave Infrared>` regions. These channels can help a model distinguish water from vegetation and some other land covers. Spatial detail matters too: a narrow stream may occupy many pixels in fine-resolution aerial imagery but only a few mixed pixels in coarser satellite imagery.

| Characteristic | VEXCEL example | Sentinel-2 example |
| --- | --- | --- |
| Platform | Aerial imagery | Satellite imagery |
| Model input | 4 channels: RGB + NIR | 6 channels: blue, green, red, NIR, SWIR1, SWIR2 |
| Spectral advantage | Adds NIR to visible imagery | Adds NIR and two SWIR bands |
| Spatial resolution | Inspect the supplied raster metadata | 10 m for visible/NIR bands and 20 m for SWIR bands *in the original sensor*; the prepared dataset uses a shared raster grid |
| Training organization | One training raster and corresponding mask | Multiple training scenes and masks |
| Training-data preparation | `export_geotiff_tiles()` | `export_geotiff_tiles_batch()` |

The central network setup is similar in both cases:

```text
architecture   = "unet"
encoder_name   = "resnet34"
num_classes    = 2          # background and water
num_channels   = 4 or 6     # must match the raster
```

Changing the number of input channels is **necessary but not sufficient**: band order, pixel-value scaling, mask encoding, and spatial alignment also need to be consistent. An encoder initialized using three-channel ImageNet weights must adapt its first layer when it receives four or six channels; GeoAI handles this for the configurations demonstrated here.

```{admonition} Before you run the workflows
:class: important
The VEXCEL image URLs were provided with this course material, but **the corresponding training-mask filename has not yet been verified**. The VEXCEL download cell deliberately requires that URL to be supplied. Do not train with an unrelated mask. The Sentinel-2 workflow has a separately published image-and-mask dataset.

Model training is most practical in a Python environment with a GPU, GeoAI (`geoai-py`), PyTorch, Rasterio, GeoPandas, and the plotting/mapping dependencies used in this lesson. A short training run demonstrates the workflow; it does not guarantee a reliable model.
```

---

## 3. Water mapping with aerial imagery

### A. Download and organize the data

We begin with a four-channel training GeoTIFF, its corresponding *reference water mask*, and an independent test image. The small test subset makes inference quicker to explore. We will keep data, training tiles, models, and results in separate folders.

```{code-cell} python
from pathlib import Path
import numpy as np
import rasterio
import matplotlib.pyplot as plt
import geoai

work_dir = Path("data")
vexcel_dir = work_dir / "vexcel"
vexcel_data = vexcel_dir / "data"
vexcel_tiles = vexcel_dir / "tiles"
vexcel_models = vexcel_dir / "models"
vexcel_results = vexcel_dir / "results"

for folder in [vexcel_data, vexcel_tiles, vexcel_models, vexcel_results]:
    folder.mkdir(parents=True, exist_ok=True)

VEXCEL_BASE = "https://data.source.coop/giuz/sds320/L07/data/"
train_url = VEXCEL_BASE + "vexcel_water_train.tif"
test_url = VEXCEL_BASE + "vexcel_water_test.tif"
mask_url = VEXCEL_BASE + "vexcel_water_mask.tif"
test_subset_url = VEXCEL_BASE + "vexcel_water_test_subset.tif"
```

Download the imagery once; subsequent cells reuse the local files. The mask is a necessary input for *supervised* learning.

```{code-cell} python
def download_once(url, folder):
    """Download url into folder unless the file already exists."""
    path = folder / Path(url).name
    if not path.exists():
        geoai.download_file(url, output_path=str(path))
    return path


vexcel_train = download_once(train_url, vexcel_data)
vexcel_test = download_once(test_url, vexcel_data)
vexcel_mask = download_once(mask_url, vexcel_data)
vexcel_subset = download_once(test_subset_url, vexcel_data)

print("VEXCEL training image:", vexcel_train)
print("VEXCEL training mask:", vexcel_mask)
print("VEXCEL test subset:", vexcel_subset)
```

### B. Inspect training data

A GeoTIFF contains both image pixels and geographic metadata. Check the number of channels, the coordinate reference system ({abbr}`CRS (Coordinate Reference System)`), the pixel size, and the data type *before* deciding which band indices to display. Band numbering in Rasterio and GeoAI begins at **1**, not 0.

```{code-cell} python
for label, path in [
    ("Training image", vexcel_train),
    ("Training mask", vexcel_mask),
    ("Test subset", vexcel_subset),
]:
    with rasterio.open(path) as src:
        print(f"\n{label}: {path.name}")
        print("  bands:", src.count, "| dimensions:", (src.height, src.width))
        print("  CRS:", src.crs, "| pixel size:", src.res)
        print("  dtype:", src.dtypes, "| nodata:", src.nodata)
        print("  band descriptions:", src.descriptions)

with rasterio.open(vexcel_train) as image, rasterio.open(vexcel_mask) as mask:
    assert image.count == 4, "Expected a four-band VEXCEL training image."
    assert mask.count == 1, "Expected one label band."
    assert image.crs == mask.crs, "The image and mask CRSs differ."
    assert image.transform.almost_equals(mask.transform, precision=1e-3), (
        "The image and mask grids differ by more than 1 mm."
    )
    assert (image.width, image.height) == (mask.width, mask.height)
    label_values = np.unique(mask.read(1))
    print("\nRaw reference-mask values:", label_values)
```

The reference mask must distinguish **background = 0** and **water = 1** for the binary model. A dataset using 255 for water or another label scheme requires conversion before tiling. Do not convert values blindly: first confirm the actual mask encoding, including any NoData pixels.

```{code-cell} python
# Inspect the labels before making any changes.
assert set(label_values.tolist()).issubset({0, 1}), (
    "Expected a binary 0/1 mask; inspect label encoding and NoData first."
)
```

```{admonition} Image values versus labels
:class: note
The four image bands contain spectral information; the single-band mask contains **class IDs**. Image values may be integers or floating-point numbers, whereas training labels should be discrete classes. Keep the mask in the same pixel grid as the imagery. For the image bands, also examine the data ranges before reusing the same model on a different acquisition.
```

The course provides VEXCEL imagery with four channels. The code below uses RGB + NIR **only after you confirm their positions from the metadata or data provenance**. If the TIFF has no usable band descriptions, confirm the order from the dataset documentation before changing the indices.

```{code-cell} python
# Set these to the actual 1-based raster band indices.
VEXCEL_RGB = [3, 2, 1]  # provisional: Red, Green, Blue
VEXCEL_FALSE_COLOR = [4, 3, 2]  # provisional: NIR, Red, Green

assert set(VEXCEL_RGB + VEXCEL_FALSE_COLOR).issubset({1, 2, 3, 4})
```

### C. Visualize the imagery

True-color imagery helps you recognize water bodies and surrounding land cover. In a NIR–red–green false-color display, water often appears dark, while actively growing vegetation may appear bright red. The overlay provides a quick visual check that the training mask follows the same shorelines as the image.

```{code-cell} python
geoai.view_raster(str(vexcel_train), indexes=VEXCEL_RGB, layer_name="VEXCEL RGB")
geoai.view_raster(
    str(vexcel_train), indexes=VEXCEL_FALSE_COLOR, layer_name="VEXCEL NIR / red / green"
)
geoai.view_raster(
    str(vexcel_mask),
    opacity=0.45,
    nodata=0,
    basemap=str(vexcel_train),
    basemap_args={"indexes": VEXCEL_RGB},
    layer_name="Reference water mask",
)
geoai.view_raster(
    str(vexcel_subset), indexes=VEXCEL_RGB, layer_name="VEXCEL unseen test subset"
)
```

**Check:** Are the mask boundaries aligned with visible shorelines? Do shadows, roofs, or dark vegetation look similar to water? These may be challenging areas for the model.

### D. Export paired training tiles

Large images are divided into square tiles so they fit into GPU memory. `tile_size=512` means each tile is 512 × 512 pixels; `stride=256` advances 256 pixels, creating overlapping samples. The exported training imagery and corresponding masks must remain aligned.

```{code-cell} python
vexcel_tile_stats = geoai.export_geotiff_tiles(
    in_raster=str(vexcel_train),
    in_class_data=str(vexcel_mask),
    out_folder=str(vexcel_tiles),
    tile_size=512,
    stride=256,
    buffer_radius=0,
    max_tiles=128,  # teaching subset; remove or increase for fuller coverage
)

vexcel_image_tiles = sorted((vexcel_tiles / "images").glob("*.tif"))
vexcel_label_tiles = sorted((vexcel_tiles / "labels").glob("*.tif"))
print("Image tiles:", len(vexcel_image_tiles))
print("Label tiles:", len(vexcel_label_tiles))
assert vexcel_image_tiles and len(vexcel_image_tiles) == len(vexcel_label_tiles)
```

The single-raster tiler writes labels into `labels/`. Examine a tile pair before training. This small Python reactivation combines a list (`vexcel_image_tiles`) with a raster reader and a simple plot.

```{code-cell} python
with rasterio.open(vexcel_image_tiles[76]) as src:
    sample_image = src.read(VEXCEL_RGB).astype("float32")
with rasterio.open(vexcel_label_tiles[76]) as src:
    sample_mask = src.read(1)

# Scale each displayed channel for visualization only.
lo = np.percentile(sample_image, 2, axis=(1, 2))[:, None, None]
hi = np.percentile(sample_image, 98, axis=(1, 2))[:, None, None]
rgb = np.clip((sample_image - lo) / np.maximum(hi - lo, 1), 0, 1)

fig, axes = plt.subplots(1, 2, figsize=(10, 4))
axes[0].imshow(np.moveaxis(rgb, 0, -1))
axes[0].set_title("Image tile")
axes[1].imshow(sample_mask, cmap="gray", vmin=0, vmax=1)
axes[1].set_title("Reference water mask")
for ax in axes:
    ax.axis("off")
plt.show()
```

```{admonition} Spatial validation is more difficult than a random split
:class: warning
Overlapping tiles from the same image contain many of the same pixels. A random tile-level training/validation split can therefore produce **optimistic** scores. We use GeoAI's convenient split here to demonstrate training, not to claim geographically independent performance. For a research project, separate training and validation geographically **before** creating overlapping tiles.
```

### E. Train a four-channel U-Net

We use a U-Net with a ResNet34 encoder, matching the architecture introduced earlier in the lesson. `num_channels=4` must agree with the VEXCEL raster. The shorter run below is a teaching demonstration; longer training may be required for useful results.

```{code-cell} python
VEXCEL_EPOCHS = 20  # increase toward 20 for a more complete experiment

geoai.train_segmentation_model(
    images_dir=str(vexcel_tiles / "images"),
    labels_dir=str(vexcel_tiles / "labels"),
    output_dir=str(vexcel_models),
    architecture="unet",
    encoder_name="resnet34",
    encoder_weights="imagenet",
    num_channels=4,
    num_classes=2,
    batch_size=4,
    num_epochs=VEXCEL_EPOCHS,
    learning_rate=0.001,
    val_split=0.2,
    seed=42,
    verbose=True,
)
```

The `batch_size` controls how many tiles are processed at once. `learning_rate` influences the size of weight updates. `val_split=0.2` holds out a fraction of tiles during training, subject to the spatial-leakage limitation described above.

### F. Inspect the learning curves

The training history can show whether loss decreases, whether the model begins to overfit, and how IoU, F1, precision, and recall evolve. High recall with low precision suggests many false water detections; low recall suggests missed water pixels.

```{code-cell} python
geoai.plot_performance_metrics(
    history_path=str(vexcel_models / "training_history.pth"),
    figsize=(15, 5),
    verbose=True,
)
```

**Interpretation:** Did the model improve throughout your short demonstration? Would more epochs plausibly help, or is validation performance already deteriorating? A validation score from these tiles is not an independent accuracy assessment of the test image.

### G. Predict water in an unseen scene

We now apply the trained model to the separate test subset using sliding windows. Overlapping inference windows reduce visible seams between neighboring predictions. The output is a **georeferenced raster mask**, not a picture with lost coordinates.

```{code-cell} python
vexcel_checkpoint = vexcel_models / "best_model.pth"
vexcel_prediction = vexcel_results / "vexcel_water_prediction.tif"

geoai.semantic_segmentation(
    input_path=str(vexcel_test),
    output_path=str(vexcel_prediction),
    model_path=str(vexcel_checkpoint),
    architecture="unet",
    encoder_name="resnet34",
    num_channels=4,
    num_classes=2,
    window_size=512,
    overlap=128,
    batch_size=4,
)

with rasterio.open(vexcel_test) as original, rasterio.open(vexcel_prediction) as pred:
    assert original.crs == pred.crs
    assert original.transform == pred.transform
    assert (original.width, original.height) == (pred.width, pred.height)
    print("Predicted mask values:", np.unique(pred.read(1)))
```

```{code-cell} python
geoai.view_raster(
    str(vexcel_prediction),
    nodata=0,
    opacity=0.55,
    basemap=str(vexcel_test),
    basemap_args={"indexes": VEXCEL_RGB},
    layer_name="Predicted water",
)
```

Look for missed ponds, incomplete shorelines, and false positives on dark roofs or shadows. Without independently labeled pixels for the test subset, this inspection is *qualitative*; it cannot provide an IoU accuracy score.

### H. Convert the mask to polygons

Raster predictions are useful for pixel-level inspection. {term}`vectorization <Vectorization>` converts connected detected areas into polygons, enabling area statistics and geographic analysis. We initially retain small polygons so that we can inspect the effects of filtering afterward.

```{code-cell} python
vexcel_raw_vector = vexcel_results / "vexcel_water_polygons_raw.geojson"
vexcel_polygons = geoai.raster_to_vector(
    raster_path=str(vexcel_prediction),
    output_path=str(vexcel_raw_vector),
    threshold=0,
    min_area=0,
    simplify_tolerance=None,
)

assert vexcel_polygons.crs is not None, "Prediction polygons need a CRS."
if not vexcel_polygons.crs.is_projected:
    raise ValueError(
        "Reproject to a suitable local metric CRS before calculating area."
    )

vexcel_polygons = geoai.add_geometric_properties(
    vexcel_polygons, area_unit="m2", length_unit="m"
)
print("Raw water polygons:", len(vexcel_polygons))
print(vexcel_polygons["area_m2"].describe())
```

The minimum-area filter below is a **demonstration choice**, not a universal rule. It removes tiny detected regions, but it can also remove genuine small water bodies.

```{code-cell} python
VEXCEL_MIN_AREA_M2 = 100
vexcel_filtered = vexcel_polygons.loc[
    vexcel_polygons["area_m2"] >= VEXCEL_MIN_AREA_M2
].copy()

vexcel_final = vexcel_results / "vexcel_water_bodies.gpkg"
vexcel_filtered.to_file(vexcel_final, driver="GPKG")
print(f"Retained {len(vexcel_filtered)} of {len(vexcel_polygons)} polygons")
print("Saved:", vexcel_final)
```

```{code-cell} python
if not vexcel_filtered.empty:
    geoai.view_vector_interactive(
        vexcel_filtered,
        tiles=str(vexcel_subset),
        tiles_args={"indexes": VEXCEL_RGB},
        layer_name="VEXCEL water polygons",
    )
    geoai.create_split_map(
        left_layer=vexcel_filtered,
        right_layer=str(vexcel_subset),
        left_args={"style": {"color": "blue", "fillOpacity": 0.3}},
        right_args={"indexes": VEXCEL_RGB},
        basemap=str(vexcel_subset),
    )
else:
    print(
        "No polygons passed the chosen filter; inspect the mask or lower the threshold."
    )
```

**VEXCEL outputs:** a model checkpoint, training history, predicted water-mask GeoTIFF, unfiltered polygon GeoJSON, and filtered water-body GeoPackage. You now have a reusable pattern for other four-channel segmentation problems.

---

## 4. Water mapping with Sentinel-2

The next dataset has six input channels and multiple training scenes. The overall sequence is familiar, but two operations change: **batch tiling** and **batch inference**.

### A. Understand the spectral bands

Sentinel-2 records 13 spectral bands; this example selects six that are useful for water mapping. The original sensor records the visible and broad NIR bands at 10 m, and the two SWIR bands at 20 m. To stack them in one GeoTIFF for machine learning, they must be represented on a common pixel grid.

| Stack band (1-based) | Sentinel-2 band | Approximate wavelength | Use in water mapping |
| ---: | --- | ---: | --- |
| 1 | B2 · blue | 490 nm | Visible-water and atmospheric information |
| 2 | B3 · green | 560 nm | Visible water/land contrast |
| 3 | B4 · red | 665 nm | Land-cover context |
| 4 | B8 · NIR | 842 nm | Strong absorption by most open water |
| 5 | B11 · SWIR1 | 1610 nm | Helps separate water from many land surfaces |
| 6 | B12 · SWIR2 | 2190 nm | Additional information about moisture and land cover |

Water can look dark in a NIR or SWIR-based false-color view because it reflects little radiation at these wavelengths. A model receives all six channels, even though we select only three for display.

### B. Inspect the Sentinel-2 dataset

We use the **Earth Surface Water Dataset** (Luo et al., 2021) from the GeoAI tutorial. The GeoAI book shows a Source Cooperative ZIP, while the currently published GeoAI example points to the same dataset hosted on Hugging Face. We use the latter here because it is the documented download link in the current example.

```{code-cell} python
s2_dir = work_dir / "sentinel2"
s2_data = s2_dir / "data"
s2_models = s2_dir / "models"
s2_results = s2_dir / "results"
for folder in [s2_data, s2_models, s2_results]:
    folder.mkdir(parents=True, exist_ok=True)

s2_url = "https://data.source.coop/giuz/sds320/L07/data/sentinel-2_water.zip"
s2_zip = s2_data / "sentinel-2_water"

# Keep the ZIP for explicit extraction in the next cell.
if not s2_zip.exists():
    geoai.download_file(s2_url, output_path=str(s2_zip), unzip=False)
```

Unzip the archive and identify the folder containing `tra_scene`, `tra_truth`, `val_scene`, and `val_truth`. Explicitly locating these directories avoids relying on a particular ZIP nesting layout.

```{code-cell} python
import zipfile

s2_extract = s2_data / "extracted"
s2_extract.mkdir(parents=True, exist_ok=True)
if not any(s2_extract.rglob("tra_scene")):
    with zipfile.ZipFile(s2_zip) as archive:
        archive.extractall(s2_extract)

s2_roots = sorted(p.parent for p in s2_extract.rglob("tra_scene") if p.is_dir())
s2_roots = [
    p
    for p in s2_roots
    if all((p / name).is_dir() for name in ["tra_truth", "val_scene", "val_truth"])
]
assert len(s2_roots) == 1, "Inspect the extracted archive structure."
s2_root = s2_roots[0]

s2_train_images = s2_root / "tra_scene"
s2_train_masks = s2_root / "tra_truth"
s2_val_images = s2_root / "val_scene"
s2_val_masks = s2_root / "val_truth"
s2_tiles = s2_dir / "tiles"

print("Dataset root:", s2_root)
for folder in [s2_train_images, s2_train_masks, s2_val_images, s2_val_masks]:
    print(folder.name, len(list(folder.glob("*.tif"))), "GeoTIFF files")
```

Confirm the spectral channels, the reference-mask classes, and image-mask correspondence. The naming pattern in this dataset identifies the same scene with different suffixes for imagery and labels.

```{code-cell} python
s2_images = sorted(s2_train_images.glob("*.tif"))
s2_masks = sorted(s2_train_masks.glob("*.tif"))
assert s2_images and len(s2_images) == len(s2_masks)

with rasterio.open(s2_images[0]) as src:
    print("Image channels:", src.count, "| shape:", (src.height, src.width))
    print("CRS:", src.crs, "| pixel size:", src.res, "| dtype:", src.dtypes)
    assert src.count == 6, "The training model requires six image bands."
with rasterio.open(s2_masks[0]) as src:
    print("Reference-mask values:", np.unique(src.read(1)))

# Check sorted image/mask pairing before the batch tiler uses it.
for image_path, mask_path in zip(s2_images, s2_masks):
    expected_mask = image_path.stem.replace("_6Bands", "") + "_Truth"
    assert mask_path.stem == expected_mask, (image_path.name, mask_path.name)
    with rasterio.open(image_path) as image, rasterio.open(mask_path) as mask:
        assert image.crs == mask.crs and image.transform == mask.transform
        assert (image.width, image.height) == (mask.width, mask.height)
```

```{admonition} Confirm how water is encoded
:class: attention
Before training, inspect the printed reference-mask values. The following GeoAI example assumes the water labels are already encoded as **background 0 / water 1**. If they are not, convert them with a documented class mapping first, while preserving the grid and NoData handling. Do not treat a 255-valued mask as class 255 in a two-class model.
```

```{code-cell} python
with rasterio.open(s2_masks[0]) as src:
    s2_label_values = np.unique(src.read(1))
assert set(s2_label_values.tolist()).issubset({0, 1}), (
    "Expected 0/1 reference labels. Investigate encoding before training."
)
```

Display the same image with true color (red–green–blue) and NIR-based false color (NIR–red–green). All indices below refer to the six-band *stack*, not to the original Sentinel-2 band numbers.

```{code-cell} python
S2_RGB = [3, 2, 1]
S2_FALSE_COLOR = [4, 3, 2]
geoai.view_raster(
    str(s2_images[0]),
    indexes=S2_RGB,
    vmin=0,
    vmax=5000,
    layer_name="Sentinel-2 true color",
)
geoai.view_raster(
    str(s2_images[0]),
    indexes=S2_FALSE_COLOR,
    vmin=0,
    vmax=5000,
    layer_name="Sentinel-2 false color",
)
```

### C. Export tiles from multiple scenes

Unlike the VEXCEL example, Sentinel-2 comes with several image-mask pairs. `export_geotiff_tiles_batch()` processes them consistently and writes their tiles to `images/` and `masks/`. The source dataset uses different suffixes for images and masks, so pairing by verified sorted order is appropriate here.

```{code-cell} python
s2_tile_stats = geoai.export_geotiff_tiles_batch(
    images_folder=str(s2_train_images),
    masks_folder=str(s2_train_masks),
    output_folder=str(s2_tiles),
    tile_size=512,
    stride=384,
    max_tiles=128,  # shorter experiment; increase for broader training
    match_by_name=False,  # sorted pairs were checked in the previous cell
    quiet=True,
)

s2_image_tiles = sorted((s2_tiles / "images").glob("*.tif"))
s2_mask_tiles = sorted((s2_tiles / "masks").glob("*.tif"))
print("Sentinel-2 training tiles:", len(s2_image_tiles))
print("Sentinel-2 label tiles:", len(s2_mask_tiles))
assert s2_image_tiles and len(s2_image_tiles) == len(s2_mask_tiles)
```

```{admonition} Training subsets and spatial coverage
:class: note
`max_tiles` keeps the demonstration manageable but may leave some landscapes underrepresented. For meaningful model assessment, document how you selected scenes and reserve geographically independent scenes for testing. More overlapping patches from one location do not replace spatial diversity.
```

### D. Train a six-channel U-Net

We keep U-Net, ResNet34, and two output classes. The main model-configuration change is `num_channels=6`, accompanied by the six-band dataset and its preprocessing. The short run below is for experimentation; the reference workflow uses more epochs for stronger convergence.

```{code-cell} python
S2_EPOCHS = 20  # increase toward 20 or more for an extended run

geoai.train_segmentation_model(
    images_dir=str(s2_tiles / "images"),
    labels_dir=str(s2_tiles / "masks"),
    output_dir=str(s2_models),
    architecture="unet",
    encoder_name="resnet34",
    encoder_weights="imagenet",
    num_channels=6,
    num_classes=2,
    batch_size=4,
    num_epochs=S2_EPOCHS,
    learning_rate=0.001,
    val_split=0.2,
    seed=42,
    verbose=True,
)
```

The higher channel count does not automatically make the model better: the usefulness of extra spectral information depends on the imagery, targets, labels, and learning setup.

### E. Evaluate training and validation

Use the same curves as in the VEXCEL workflow. This makes model diagnostics familiar even though the sensor and dataset changed.

```{code-cell} python
geoai.plot_performance_metrics(
    history_path=str(s2_models / "training_history.pth"),
    figsize=(15, 5),
    verbose=True,
)
```

The original book reports results from its own training run. Your values may differ because you use a shortened training configuration and a potentially different tile subset. Do not compare these validation metrics directly with VEXCEL: these are **different datasets and test conditions**.

### F. Run batch inference

Apply the best checkpoint to the dataset's separate validation scenes. `semantic_segmentation_batch()` predicts one georeferenced mask per image using consistent window and model settings.

```{code-cell} python
s2_checkpoint = s2_models / "best_model.pth"
s2_predictions = s2_results / "predictions"
s2_predictions.mkdir(parents=True, exist_ok=True)

geoai.semantic_segmentation_batch(
    input_dir=str(s2_val_images),
    output_dir=str(s2_predictions),
    model_path=str(s2_checkpoint),
    architecture="unet",
    encoder_name="resnet34",
    num_channels=6,
    num_classes=2,
    window_size=512,
    overlap=256,
    batch_size=4,
    quiet=True,
)

print("Predictions:", len(list(s2_predictions.glob("*.tif"))))
```

### G. Compare prediction with reference

Select a held-out scene and find the prediction and reference mask with the same scene identifier. Then compare the input image, the predicted mask, and the reference. We use the visible/NIR false-color composite to aid interpretation.

```{code-cell} python
s2_val_files = sorted(s2_val_images.glob("*.tif"))
assert s2_val_files
s2_val_image = s2_val_files[0]
s2_scene_id = s2_val_image.stem.replace("_6Bands", "")

s2_truth_matches = sorted(s2_val_masks.glob(f"{s2_scene_id}_Truth.tif"))
s2_pred_matches = sorted(s2_predictions.glob(f"{s2_val_image.stem}*mask*.tif"))
assert len(s2_truth_matches) == 1, (
    "Could not uniquely identify the matching reference mask."
)
assert len(s2_pred_matches) == 1, "Could not uniquely identify the predicted mask."

s2_truth = s2_truth_matches[0]
s2_prediction = s2_pred_matches[0]
with rasterio.open(s2_val_image) as image, rasterio.open(s2_prediction) as pred:
    assert image.crs == pred.crs and image.transform == pred.transform

geoai.plot_prediction_comparison(
    original_image=str(s2_val_image),
    prediction_image=str(s2_prediction),
    ground_truth_image=str(s2_truth),
    titles=["Sentinel-2", "Predicted water", "Reference water"],
    indexes=S2_FALSE_COLOR,
    divider=5000,
    figsize=(15, 5),
    show_plot=True,
    save_path=str(s2_results / "sentinel2_water_comparison.png"),
)
```

**Look closely:** Are lake boundaries pixelated? Are rivers too narrow to resolve reliably? A pixel near a shoreline may contain both water and land, producing a *mixed pixel*. A reference mask provides a basis for evaluation only if its labels and the prediction grid are correct.

### H. Vectorize and calculate area

Convert the selected scene's predicted mask to polygons. As before, calculate areas only in a suitable **projected metric CRS**. The same vector workflow works for the six-band model because its output is still a one-band classification raster.

```{code-cell} python
s2_raw_vector = s2_results / "sentinel2_water_polygons_raw.geojson"
s2_polygons = geoai.raster_to_vector(
    raster_path=str(s2_prediction),
    output_path=str(s2_raw_vector),
    threshold=0,
    min_area=0,
    simplify_tolerance=None,
)

assert s2_polygons.crs is not None
if not s2_polygons.crs.is_projected:
    raise ValueError(
        "Reproject to a suitable local metric CRS before calculating area."
    )

s2_polygons = geoai.add_geometric_properties(
    s2_polygons, area_unit="m2", length_unit="m"
)

S2_MIN_AREA_M2 = 100
s2_filtered = s2_polygons.loc[s2_polygons["area_m2"] >= S2_MIN_AREA_M2].copy()

s2_final = s2_results / "sentinel2_water_bodies.gpkg"
s2_filtered.to_file(s2_final, driver="GPKG")
print("Retained polygons:", len(s2_filtered))
print("Saved:", s2_final)
```

A 100 m² feature may represent very different numbers of pixels at VEXCEL and Sentinel-2 resolution. Always relate the area threshold to pixel size and the smallest water body your project needs to detect.

### I. Visualize detected water bodies

Create an interactive polygon overlay and, where supported, a split comparison. You can also examine the distribution of detected polygon areas.

```{code-cell} python
if not s2_filtered.empty:
    geoai.view_vector_interactive(
        s2_filtered,
        column="area_m2",
        tiles=str(s2_val_image),
        tiles_args={"indexes": S2_FALSE_COLOR, "vmin": 0, "vmax": 5000},
        layer_name="Sentinel-2 water bodies",
    )
    geoai.create_split_map(
        left_layer=s2_filtered,
        right_layer=str(s2_val_image),
        left_args={"style": {"color": "blue", "fillOpacity": 0.3}},
        right_args={"indexes": S2_FALSE_COLOR, "vmin": 0, "vmax": 5000},
        basemap=str(s2_val_image),
    )
    print(s2_filtered["area_m2"].describe())
else:
    print("No water polygons passed the current minimum-area filter.")
```

**Sentinel-2 outputs:** a six-channel model checkpoint, training history, predicted validation-scene masks, a comparison figure, raw polygons, and a final GeoPackage. The processing steps are recognizable from VEXCEL, but the spectral input and data organization differ.

---

## 5. Comparing the workflows

We used U-Net for the *same classification problem*—water versus background—but trained it with different sensors and datasets.

| Decision | Four-band VEXCEL | Six-band Sentinel-2 |
| --- | --- | --- |
| Useful strength | Fine geometric detail in the supplied aerial image | Additional SWIR information about surface materials |
| Potential limitation | Different acquisition conditions and small-scale visual complexity | Mixed pixels and reduced detail at small water features |
| Tiling function | Single image and corresponding mask | Batch of paired image/mask scenes |
| Model configuration | `num_channels=4` | `num_channels=6` |
| Inference | One test image or subset | Batch of validation scenes |
| Typical use case | Precise local delineation | Mapping over wider areas or across scenes |

The **physical properties of water** help explain why NIR and SWIR can support detection. The **size of the target** helps explain why spatial resolution matters. Neither richer spectral coverage nor finer pixels guarantee better segmentation independently of training quality and site conditions.

```{admonition} Different datasets are not a controlled sensor comparison
:class: warning
The VEXCEL and Sentinel-2 models were not trained and evaluated on the same labeled locations. Their validation IoU or F1 scores should **not** be ranked as proof that one sensor outperforms the other. A controlled comparison would need matched study areas, consistent reference labels, defined spatial splits, and an evaluation protocol that accounts for different spatial resolutions.
```

For your SDS320 project, ask: *How small are my target features? Which bands are available? Can I obtain reliable training masks? Will the model be applied to another date, location, or sensor?*

---

## 6. Common pitfalls

- **Wrong band order.** A model trained on RGB + NIR cannot interpret a different band order correctly. Verify metadata and provenance, and keep the training and inference conventions consistent.
- **Different scaling.** Raw digital numbers or reflectance ranges may differ across datasets; a mismatch between training and inference can cause poor predictions even if the band count agrees.
- **Wrong label classes.** A raster with water encoded as 255 is not automatically a 0/1 mask. Confirm the values and NoData treatment before tiling.
- **Misaligned image and labels.** Matching file dimensions does not guarantee matching georeferencing. Check CRS, pixel grid, and transform.
- **Spatial data leakage.** Overlapping tiles randomly allocated to training and validation can inflate apparent performance. Split by geography for independent assessment.
- **Overinterpreting short runs.** Five epochs demonstrate the pipeline; they may be insufficient for convergence.
- **False water detections.** Shadows, dark roofs, and other low-reflectance surfaces can confuse a model. Inspect outputs in context.
- **Overfiltering small features.** A minimum-area threshold may remove real ponds or streams. Set it using the sensor resolution and project objective.
- **Incorrect area units.** Calculate polygon areas in an appropriate projected metric CRS, not directly in longitude and latitude.

---

## 7. Mini task

**Exploration (10–20 minutes):** Choose one completed workflow and investigate how the minimum-area threshold changes its final water-body dataset. You do **not** need to retrain a model.

1. Count the predicted water polygons **before** filtering.
2. Compare the polygon count and summed area using thresholds of **0**, **100**, and **500 m²**.
3. Identify one type of real water feature that an aggressive threshold might remove.
4. Decide which threshold, if any, is defensible for your own project, and explain why.

:::{note} Sample solution
:class: dropdown

```python
# The demonstration uses the VEXCEL polygons created above.
for threshold in [0, 100, 500]:
    kept = vexcel_polygons.loc[vexcel_polygons["area_m2"] >= threshold]
    area = kept["area_m2"].sum()
    print(f"{threshold:>3} m²: {len(kept):>4} polygons, {area:,.0f} m²")
```

The polygon count cannot increase as the minimum area threshold becomes larger. Total retained area will also stay the same or decrease. The exact values depend on **your model predictions**, so no fixed numerical answer is expected. A stringent filter may remove small ponds, narrow water features, and isolated false detections alike. Choose the threshold based on the smallest valid target feature and inspect the removed polygons, rather than using a single value for every project.

:::

---

## 8. Key takeaways

- The same supervised segmentation workflow can be adapted to VEXCEL (four channels) and Sentinel-2 (six channels), with appropriate changes to preprocessing and `num_channels`.
- NIR and SWIR provide useful information for water detection; spatial resolution determines how well small features and shorelines can be represented.
- Training and inference require consistent band order, value scaling, label encoding, and georeferencing.
- Georeferenced predictions can be converted to polygons for area statistics, filtering, and interactive mapping.
- Evaluation must account for spatial independence and reference-label quality; scores from different datasets are not a fair sensor ranking.

### Further reading

- GeoAI, [Train a Semantic Segmentation Model for Water Detection](https://opengeoai.org/examples/water_detection/) — companion example for the supervised workflow.
- GeoAI, [Water Detection with Sentinel-2 Imagery](https://opengeoai.org/examples/water_detection_s2/) — complete six-band example and source of the Sentinel-2 dataset download.
- GeoAI, [Train a Semantic Segmentation Model](https://opengeoai.org/examples/train_segmentation_model/) — model configuration, inference, and performance diagnostics.
- TorchGeo, [Earth Surface Water](https://docs.torchgeo.org/en/stable/tutorials/earth_surface_water.html) — dataset background and a complementary approach to surface-water learning.

In the next chapter, you will meet a different strategy: applying a water-segmentation model without training a new model yourself.
