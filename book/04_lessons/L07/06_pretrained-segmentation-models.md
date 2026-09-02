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

The previous two pages trained models from scratch on labeled data. Sometimes that is unnecessary: a well-suited {term}`pre-trained model <Pre-Trained Model>` can produce a usable mask with no custom training data or training time at all. Knowing when a ready-made, {term}`sensor-agnostic model <Sensor-Agnostic Model>` is a reasonable shortcut, and knowing that "pre-trained" does not mean "beyond question," is a practical skill in its own right, separate from knowing how to train a model yourself.

---

## 2. Core idea

A sensor-agnostic pre-trained model has already been trained, generally on a large and diverse dataset, to perform one specific task (water masking, cloud detection) across multiple sensor types. You supply your imagery and a description of its band order; the model returns a mask. This removes the data-preparation and training steps from your workflow.

```{admonition} Inference-only still requires evaluation
:class: important
Skipping training does not mean skipping evaluation. A pre-trained model can still fail on imagery unlike anything in its training data, and you have no training-loss curve to warn you when that happens. Spot-checking outputs against what you can see in the imagery becomes even more important.
```

---

## 3. Two sensor-agnostic examples

### A. OmniWaterMask: Water segmentation

{term}`OmniWaterMask` combines a sensor-agnostic deep learning model with a spectral water index and OpenStreetMap reference data, and supports multiple sensors including Sentinel-2, NAIP, Landsat, and others, all without any custom training.

```{code-cell} python
s2_path = geoai.download_file(
    "https://data.source.coop/opengeos/geoai/S2A-L2A-20190318-N0211-R061-6Bands-S2.tif"
)

s2_gdf = geoai.segment_water(
    s2_path,
    band_order="sentinel2",
    output_raster="s2_owm_water_mask.tif",
    output_vector="s2_owm_water_bodies.geojson",
    smooth=True,
    smooth_iterations=3,
    min_size=100,
)
```

`band_order="sentinel2"` tells the function how to map your specific input raster's bands to the red, green, blue, and NIR channels the underlying model expects; a `"naip"` preset does the same for NAIP's native band order. Passing `output_vector` runs vectorization and polygon smoothing in the same call, giving you a ready-to-use `GeoDataFrame` directly, without a separate {term}`vectorization <Vectorization>` step.

The source material also applies the water workflow to four-band NAIP imagery.

```{code-cell} python
naip_url = (
    "https://data.source.coop/opengeos/geoai/"
    "naip_water_test_subset.tif"
)

naip_path = geoai.download_file(naip_url)
```

The band-order preset changes:

```{code-cell} python
naip_gdf = geoai.segment_water(
    naip_path,
    band_order="naip",
    output_raster="naip_owm_water_mask.tif",
    output_vector="naip_owm_water_bodies.geojson",
    smooth=True,
    smooth_iterations=3,
    min_size=100,
)
```

The model is reused, but the sensor-specific band mapping remains explicit.

### B. OmniCloudMask: Cloud segmentation

Cloud contamination affects nearly all optical satellite analysis, which makes cloud masking a common, high-value use case for a pre-trained model. {term}`OmniCloudMask` is sensor-agnostic and needs only red, green, and NIR bands, classifying every pixel into one of four categories:

| Value | Class |
| ---: | --- |
| 0 | Clear |
| 1 | Thick cloud |
| 2 | Thin cloud |
| 3 | Cloud shadow |

For the sample Sentinel-2 raster:

```{code-cell} python
url = (
    "https://data.source.coop/opengeos/geoai/"
    "S2C-MSIL2A-20250920T162001-subset.tif"
)

s2_path = geoai.download_file(url)
```

Run:

```{code-cell} python
pred_path = "cloud_mask.tif"

geoai.predict_cloud_mask_from_raster(
    input_path=s2_path,
    output_path=pred_path,
    red_band=1,
    green_band=2,
    nir_band=4,
    batch_size=4,
    inference_dtype="bf16",
)
```

`red_band`, `green_band`, and `nir_band` map your specific raster's band indices to what the model expects, the same kind of mapping `band_order` handled for water segmentation. `geoai.calculate_cloud_statistics()` then summarizes what fraction of the scene falls into each of the four classes, useful for quickly deciding whether a scene is even worth analyzing further.

```{code-cell} python
import rasterio as rio

with rio.open(pred_path) as src:
    pred_array = src.read(1)

stats = geoai.calculate_cloud_statistics(pred_array)
for key, value in stats.items():
    if "percent" in key:
        print(f"{key}: {value:.2f}%")
    else:
        print(f"{key}: {value:,}")

cloud_free = geoai.create_cloud_free_mask(pred_array)
```

`geoai.create_cloud_free_mask()` then produces a simple usable/not-usable binary mask from these four classes, which you can use to screen out heavily clouded scenes before running any downstream analysis, such as the land-cover classification on the next page.

---

## 4. What still needs checking

Before trusting a pre-trained model's output for a project deliverable, check at least three things: that your imagery's sensor and resolution genuinely fall within what the model was designed for, that your `band_order` (or equivalent band-index arguments) actually matches your raster, and that a visual spot-check of the output against the source imagery looks reasonable, especially in regions your project cares about most. None of these checks require retraining anything; they simply require the same skepticism you would apply to a model you trained yourself.

---

## 5. Python reactivation

`stats.items()` iterates over a dictionary's key-value pairs together, the same pattern used for `splits` dictionaries in earlier lessons, here used to format percentage values differently from raw pixel counts within a single loop.

---

## 6. Common pitfalls

- **Assuming "pre-trained" means "always correct for my imagery."** A sensor-agnostic model still has limits; imagery far outside its training distribution (unusual sensors, extreme conditions) can still produce poor results.
- **Getting `band_order` or band-index arguments wrong.** This is the most common source of nonsensical output from an otherwise correct call; if results look clearly wrong, check band mapping before suspecting the model itself.
- **Treating the four cloud classes as fixed for every use case.** Whether thin clouds or shadows count as "usable" depends on your downstream analysis; `create_cloud_free_mask()` lets you adjust this rather than accepting one default definition of "usable."
- **Skipping the visual spot-check because no training was involved.** Inference-only workflows still produce wrong answers sometimes; the absence of a training step is not evidence of correctness.

---

## 7. Mini task

You need a water mask for a Landsat scene over an area with many small, seasonal ponds that OmniWaterMask has not been specifically validated on. What would you check before relying on the output for a project deliverable, and what would make you decide to train a custom model instead?

:::{dropdown} Sample solution
:class: note

You would check whether OmniWaterMask's supported sensors and resolution range genuinely include Landsat at the relevant resolution, confirm the correct band order for Landsat's specific band layout, and visually compare the output mask against a few ponds you can identify by eye in the source imagery, particularly the smallest ones, since those are most likely to be missed. If the visual check shows the model consistently missing the small seasonal ponds that matter most for the project, that would be a reasonable signal to invest in a custom-trained model (following the earlier pages in this lesson) rather than relying on the pre-trained one.
:::

---

## 8. Key takeaways

- Sensor-agnostic pre-trained models remove the need for custom training data and training time, at the cost of less control over exactly what the model has learned.
- `geoai.segment_water()` and `geoai.predict_cloud_mask_from_raster()` both need you to correctly describe your imagery's band order or band indices; this is the most common point of failure.
- Skipping training does not remove your responsibility to check outputs; visual spot-checks and sensor/resolution compatibility checks remain necessary.
- Cloud masking is a common and valuable use of a pre-trained model, often as a preprocessing step before other analyses, including the land-cover classification on the next page.

### Further reading

- OpenGeoAI, ["Load Model Checkpoint"](https://opengeoai.org/examples/load_model_checkpoint/) — general guidance on loading and reusing existing model checkpoints, relevant whenever you rely on someone else's trained weights.
- OpenGeoAI, ["Cloud and Cloud Shadow Detection"](https://opengeoai.org/examples/cloud_detection/) — notebook demonstrating the integration of OmniCloudMask in GeoAI.
- OpenGeoAI, ["Segment Anything (samgeo)"](https://opengeoai.org/examples/samgeo/) — another pre-trained, general-purpose segmentation approach, covered in more depth in L12.
