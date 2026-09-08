---
site:
  outline_maxdepth: 2
---

# Regression data and tiles

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Preparing continuous-value labels and tiles for regression
</div>
<!-- markdownlint-enable MD033 -->

---

You know which architecture and loss function to reach for. This page prepares the data those choices will train on: Landsat imagery paired with NDVI, tiled into matched patches ready for `geoai.train_pixel_regressor()` on the next page.

---

## 1. Motivation

Regression tiling looks similar to the segmentation tiling from L07, but the target side is different in a way that matters: instead of a categorical mask, you are pairing each image tile with a continuous-valued target tile. Getting this pairing, and the valid-range filtering that goes with it, right here saves you from diagnosing subtle training problems later.

---

## 2. Core idea

A regression dataset consists of matched pairs: an image tile and a target tile covering the exact same location, where the target tile stores continuous values (here, NDVI) rather than class indices. `geoai.create_regression_tiles()` handles this pairing, tiling, and valid-range filtering in one call.

---

## 3. Workflow

### A. Download imagery and target data

```{code-cell} python
import geoai

train_raster = geoai.download_file(
    "https://data.source.coop/opengeos/geoai/tn_landsat_2022.tif"
)
train_target = geoai.download_file(
    "https://data.source.coop/opengeos/geoai/tn_ndvi_2022.tif"
)
test_raster = geoai.download_file(
    "https://data.source.coop/opengeos/geoai/tn_landsat_2023.tif"
)
```

This is {term}`Landsat` imagery over Knoxville, Tennessee, paired with an {term}`NDVI <NDVI (Normalized Difference Vegetation Index)>` raster for the same area and year (2022), used for training. A separate 2023 Landsat scene is downloaded too, held out for the temporal-prediction workflow two pages from now.

### B. Inspect the data before tiling

```{code-cell} python
import rasterio

with rasterio.open(train_raster) as src:
    in_channels = src.count
    print(f"Input shape: {src.height} x {src.width}, {src.count} bands")
    print(f"Input CRS: {src.crs}")
    print(f"Input resolution: {src.res}")

with rasterio.open(train_target) as src:
    print(f"Target shape: {src.height} x {src.width}, {src.count} band(s)")
    target_data = src.read(1)
    print(f"Target value range: [{target_data.min():.2f}, {target_data.max():.2f}]")
```

Checking the target's actual value range before tiling is worth doing every time: NDVI should fall roughly within −1 to 1, and a range far outside that would be a sign of a data problem worth investigating before you spend any time training.

### C. Create paired training tiles

```{code-cell} python
image_paths, target_paths = geoai.create_regression_tiles(
    input_raster=train_raster,
    target_raster=train_target,
    output_dir="ndvi_tiles",
    tile_size=256,
    stride=128,
    target_band=1,
    min_valid_ratio=0.9,
    target_min=-1.0,
    target_max=1.0,
)

print(f"Created {len(image_paths)} tiles")
```

`tile_size=256` with `stride=128` creates 50% overlapping tiles, increasing the effective number of training samples, the same overlap idea used for detection and segmentation tiling in earlier lessons. `target_min=-1.0` and `target_max=1.0` clip target values to NDVI's valid physical range during tile creation, keeping obviously invalid values out of the training data. `min_valid_ratio=0.9` filters out tiles where more than 10% of pixels are nodata, avoiding training on tiles that are mostly missing data.

### D. Split into training and validation sets

```{code-cell} python
from sklearn.model_selection import train_test_split

train_imgs, val_imgs, train_tgts, val_tgts = train_test_split(
    image_paths, target_paths, test_size=0.2, random_state=42
)

print(f"Training: {len(train_imgs)}, Validation: {len(val_imgs)}")
```

An 80/20 split is a reasonable default, balancing enough training data against a validation set large enough to give a reliable signal during training, covered next page.

---

## 4. Python reactivation

`with rasterio.open(...)` is the same context-manager pattern you have used throughout this course for reading raster metadata and arrays. `train_test_split()` is the same `scikit-learn` function used for splitting data in earlier lessons; here it operates on lists of file paths rather than arrays directly, splitting the image and target path lists together so each image tile stays correctly paired with its matching target tile after the split.

---

## 5. Common pitfalls

- **Skipping `target_min`/`target_max` filtering.** Without it, any noisy or erroneous values in your reference raster flow directly into training, which can distort what the model learns.
- **Setting `min_valid_ratio` too low.** This allows tiles that are mostly nodata into your training set, adding noise without adding real signal.
- **Forgetting that overlapping tiles (via `stride` smaller than `tile_size`) are not entirely independent samples.** They increase your effective dataset size, but neighboring tiles share real pixels, worth keeping in mind if you later want a stricter, non-overlapping evaluation set.
- **Not checking the target's value range before tiling.** A quick check, as in step B, catches a data problem before it propagates into hours of training.

---

## 6. Mini task

`create_regression_tiles()` reports "Created 3,200 tiles" with `tile_size=256` and `stride=128`. If you instead used `stride=256` (no overlap), would you expect more, fewer, or the same number of tiles from the same input raster? Explain.

:::{dropdown} Sample solution
:class: note

Fewer tiles. With `stride=256` equal to `tile_size`, each tile covers a distinct, non-overlapping region, so the raster is divided into the minimum number of tiles needed to cover it once. With `stride=128` (half of `tile_size`), tiles overlap by 50%, roughly doubling the number of tiles extracted from the same raster area, which is exactly why the workflow above uses the smaller stride, to increase the effective training set size.
:::

---

## 7. Key takeaways

- `create_regression_tiles()` produces matched image and target tile pairs, the regression equivalent of the segmentation tiling from L07.
- `target_min`/`target_max` enforce a valid physical range during tile creation; `min_valid_ratio` filters out tiles with too much missing data.
- Overlapping tiles (via a smaller `stride`) increase the effective training set size, though neighboring tiles are not fully independent samples.
- Checking your target raster's actual value range before tiling is a fast way to catch data problems early.
