---
site:
  outline_maxdepth: 2
---

# Traditional approaches

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Simpler, non-deep-learning approaches to detecting change
</div>
<!-- markdownlint-enable MD033 -->

---

You now know what makes two images comparable. This page uses two Landsat scenes over Knoxville, Tennessee, to run the simplest change detection methods available: subtracting pixel values, and comparing multi-band change vectors. Both work without any labeled training data, which makes them a useful baseline before the deep learning workflow later in this lesson.

---

## 1. Motivation

Not every project has, or needs, a trained deep learning model. Traditional methods are fast, fully interpretable (you can explain exactly why a pixel was flagged), and often good enough for a first look at a study area, or for projects where labeled training data simply is not available.

---

## 2. Core idea

Both methods treat every pixel independently: they compare spectral values between two dates and flag pixels whose difference exceeds a threshold. This makes them simple and transparent, but it also means they have no way to use spatial context, a limitation the next page's deep learning approach addresses directly.

---

## 3. Workflow

### A. Download and read two-date imagery

```{code-cell} python
import numpy as np
import matplotlib.pyplot as plt
import rasterio
import geoai

url_2023 = "https://data.source.coop/opengeos/geoai/knoxville_landsat_2023.tif"
url_2024 = "https://data.source.coop/opengeos/geoai/knoxville_landsat_2024.tif"
path_2023 = geoai.download_file(url_2023)
path_2024 = geoai.download_file(url_2024)

# Read the NIR band (band 5 in Landsat 8/9) from both dates
with rasterio.open(path_2023) as src:
    nir_2023 = src.read(5).astype(np.float32)
with rasterio.open(path_2024) as src:
    nir_2024 = src.read(5).astype(np.float32)
```

This is a {term}`Landsat` scene from two different years over the same area. Reading the same band from both dates, here {term}`near-infrared (NIR) <Near-Infrared>`, is the starting point for image differencing, since NIR reflectance is particularly sensitive to changes in vegetation and built structures.

### B. Image differencing

**{term}`Image differencing <Image Differencing>`** subtracts one date's pixel values from the other's, then thresholds the result.

```{code-cell} python
diff = nir_2024 - nir_2023

# A threshold based on a fixed number of standard deviations
threshold = 2 * np.std(diff)
change_mask = np.abs(diff) > threshold

print(f"Difference range: {diff.min():.2f} to {diff.max():.2f}")
print(f"Threshold: {threshold:.2f}")
print(f"Changed pixels: {change_mask.sum():,} ({100 * change_mask.mean():.1f}%)")
```

Pixels with large absolute differences (positive or negative) are classified as changed; everything else is left as unchanged. This is the simplest possible change detection method, and it is worth visualizing all three pieces together: the raw difference, its histogram, and the resulting mask.

```{code-cell} python
fig, axes = plt.subplots(1, 3, figsize=(15, 5))

axes[0].imshow(diff, cmap="RdBu", vmin=-threshold * 2, vmax=threshold * 2)
axes[0].set_title("NIR Difference (2024 - 2023)")
axes[0].axis("off")

axes[1].hist(diff.ravel(), bins=100, color="steelblue")
axes[1].axvline(-threshold, color="red", linestyle="--", label=f"Threshold (±{threshold:.2f})")
axes[1].axvline(threshold, color="red", linestyle="--")
axes[1].set_title("Difference Histogram")
axes[1].legend()

axes[2].imshow(change_mask, cmap="Reds")
axes[2].set_title(f"Change Mask ({100 * change_mask.mean():.1f}% changed)")
axes[2].axis("off")

plt.tight_layout()
plt.show()
```

### C. Change Vector Analysis

**{term}`Change Vector Analysis (CVA) <Change Vector Analysis>`** extends the same idea to multiple bands at once. Instead of one difference value per pixel, each pixel becomes a vector in multi-band spectral space, with a magnitude (how much it changed) and a direction (what kind of change it likely represents).

```{code-cell} python
with rasterio.open(path_2023) as src:
    red_2023 = src.read(4).astype(np.float32)
    nir_2023 = src.read(5).astype(np.float32)
with rasterio.open(path_2024) as src:
    red_2024 = src.read(4).astype(np.float32)
    nir_2024 = src.read(5).astype(np.float32)

delta_red = red_2024 - red_2023
delta_nir = nir_2024 - nir_2023

magnitude = np.sqrt(delta_red**2 + delta_nir**2)
direction = np.degrees(np.arctan2(delta_nir, delta_red))

mag_threshold = np.percentile(magnitude, 95)
significant_change = magnitude > mag_threshold

print(f"CVA magnitude range: {magnitude.min():.2f} to {magnitude.max():.2f}")
print(f"95th percentile threshold: {mag_threshold:.2f}")
print(f"Significant change pixels: {significant_change.sum():,}")
```

The direction component is what makes CVA more informative than single-band differencing: a transition from vegetation to bare soil produces a change vector pointing in a characteristic direction (rising red reflectance, falling NIR), which means you can begin to distinguish *kinds* of change from the vector direction, without needing any labeled training examples.

### D. Choosing a threshold

Both methods depend entirely on the threshold you choose. A threshold that is too low floods the result with noise; one that is too high misses real changes. Common strategies include a fixed number of standard deviations (as used above), a percentile cutoff, automatic methods like Otsu's thresholding, or empirical tuning against a small number of known change locations.

---

## 4. Python reactivation

`np.std(diff)` and `np.percentile(magnitude, 95)` are the same summary-statistics functions you have used on raster arrays throughout this course, applied here directly as thresholding rules. `np.arctan2(delta_nir, delta_red)` computes an angle from two components, the standard way to get a direction from a two-dimensional vector, and `np.degrees()` converts the result from radians to degrees for easier interpretation.

---

## 5. Common pitfalls

- **Treating the threshold as a fixed, universal setting.** It depends on your specific data and noise level; always inspect the histogram, as in step B, rather than trusting a default blindly.
- **Using only one band when multi-band information is available.** CVA's direction component adds real interpretive value over single-band differencing whenever you have more than one comparable band.
- **Forgetting these methods are pixel-independent.** Neither technique uses any spatial context; an isolated noisy pixel is treated identically to a pixel in the middle of a genuine change area. This limitation motivates the deep learning approach on the next page.
- **Skipping the comparability checks from the previous page.** Traditional methods are especially sensitive to co-registration and phenological mismatches, since they have no spatial or learned context to fall back on.

---

## 6. Mini task

Using the image-differencing output above, what would happen to `change_mask.sum()` if you changed the threshold from `2 * np.std(diff)` to `1 * np.std(diff)`? Reason through the direction of the change before checking.

:::{dropdown} Sample solution
:class: note

Lowering the threshold from 2 standard deviations to 1 makes the criterion for "changed" easier to satisfy, so `change_mask.sum()` should increase substantially, since more pixels with moderate differences will now exceed the lower bar. This would likely include more genuine changes, but also more noise-driven false positives, exactly the threshold trade-off described in step D.
:::

---

## 7. Key takeaways

- Image differencing subtracts pixel values between two dates and thresholds the result; it is simple, interpretable, and needs no training data.
- Change Vector Analysis extends differencing to multiple bands, adding a direction component that hints at the type of change, not just its presence.
- Threshold choice controls the trade-off between missed changes and false alarms; inspect the data (histogram, percentile distribution) rather than guessing.
- Both methods treat pixels independently, ignoring spatial context, which is exactly what deep learning approaches, covered next, are designed to address.

### Further reading

- OpenGeoAI, ["Change Detection"](https://opengeoai.org/examples/change_detection/) — a related worked example of traditional change detection with `geoai`.
