---
site:
  outline_maxdepth: 2
---

# Multispectral workflows

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Adapting segmentation workflows to multispectral and sensor-specific inputs
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why input data matters

The previous page used three-channel aerial imagery to segment buildings. The same segmentation architecture can be used with very different forms of imagery, but the input configuration needs to match the sensor. The source material illustrates this with surface-water mapping from:

1. standard RGB imagery,
2. six-band {term}`Sentinel-2` imagery,
3. four-band NAIP imagery.

The central lesson is not that one sensor is universally better. It is that the model can only learn from information contained in the selected image channels at the available {term}`Spatial Resolution`.

---

## 2. Core idea

The segmentation workflow itself barely changes across sensors: the same `train_segmentation_model()` and `semantic_segmentation()` calls work whether your input has 3, 4, or 6 bands. The one parameter that must match your data is `num_channels`. Everything else, tiling, training loop, evaluation, stays the same, which is what makes comparing sensors and band counts a matter of changing one setting rather than rebuilding a pipeline.

---

## 3. Three configurations, one workflow

### A. RGB imagery: the baseline

Standard three-band RGB imagery (JPG or PNG, without embedded geographic coordinates) is a natural starting point: it isolates the core computer-vision problem from any geospatial complexity, and techniques learned on it transfer directly once you move to georeferenced data.

The model configuration contains:

```text
num_channels = 3
num_classes  = 2
```

A typical training call is:

```{code-cell} python
geoai.train_segmentation_model(
    images_dir=f"{out_folder}/images",
    labels_dir=f"{out_folder}/masks",
    output_dir=f"{out_folder}/unet_models",
    architecture="unet",
    encoder_name="resnet34",
    encoder_weights="imagenet",
    num_channels=3,
    num_classes=2,
    batch_size=16,
    num_epochs=20,
    learning_rate=0.001,
    val_split=0.2,
    target_size=(512, 512),
    verbose=True,
)
```

On a global waterbody dataset of RGB image-mask pairs, this configuration reaches a best validation IoU around 0.71. RGB alone captures spectral and textural contrasts between water and land, which works reasonably well but leaves useful information on the table.

### B. Sentinel-2 multispectral imagery

The Sentinel-2 provides {term}`multispectral imagery <Multispectral Imagery>` in 13 spectral bands including these six major bands:

| Band | Approximate wavelength | Relevance to water |
| --- | ---: | --- |
| Blue | 490 nm | Visible-water and atmospheric information |
| Green | 560 nm | Visible contrast and water appearance |
| Red | 665 nm | Land-water contrast |
| NIR | 842 nm | Strong water absorption |
| SWIR1 | 1610 nm | Strong separation of water from many land surfaces |
| SWIR2 | 2190 nm | Additional separation from wet soil and vegetation |

Water absorbs strongly in the {term}`near-infrared (NIR) <Near-Infrared (NIR)>` and {term}`shortwave infrared (SWIR) <Shortwave Infrared (SWIR)>`, producing very low reflectance in these bands compared to vegetation and soil. Including them as extra input channels lets the model exploit a physical property of water that is simply invisible in RGB alone.

```{code-cell} python
geoai.train_segmentation_model(
    images_dir="dset-s2/tiles/images",
    labels_dir="dset-s2/tiles/masks",
    output_dir="dset-s2/tiles/unet_models",
    architecture="unet",
    encoder_name="resnet34",
    encoder_weights="imagenet",
    num_channels=6,
    num_classes=2,
    batch_size=32,
    num_epochs=20,
    learning_rate=0.001,
    val_split=0.2,
)
```

The only change from the RGB configuration is `num_channels=6`, corresponding to blue, green, red, NIR, SWIR1, and SWIR2 bands. On the Earth Surface Water Dataset, this configuration reaches a best validation IoU around 0.90, a marked improvement, largely attributable to the additional spectral information rather than to anything else in the pipeline.

Select three bands to create an {term}`RGB Composite`. The source Sentinel-2 example uses:

```{code-cell} python
geoai.view_raster(
    s2_path,
    indexes=[6, 4, 3],
    vmin=0,
    vmax=5000,
    layer_name="Sentinel-2",
)
```

```{admonition} What happens when num_channels changes?
:class: note
When the input channel count differs from the 3 channels used in the encoder's ImageNet pre-training, `geoai` automatically adapts the first convolutional layer: the existing RGB weights are reused where they still apply, and the extra channels are initialized separately. You do not need to handle this adaptation yourself, but it is worth knowing that it is happening, since it is part of why transfer learning still helps even with a 6-band input.
```

### C. NAIP aerial imagery

{term}`NAIP` provides 4-band (red, green, blue, NIR) aerial imagery at 1-meter resolution. At this resolution, the model can resolve narrow streams, small ponds, and detailed shoreline geometry invisible at Sentinel-2's 10-to-20-meter resolution.

```{code-cell} python
geoai.train_segmentation_model(
    images_dir="naip/images",
    labels_dir="naip/labels",
    output_dir="naip/models",
    architecture="unet",
    encoder_name="resnet34",
    encoder_weights="imagenet",
    num_channels=4,
    num_classes=2,
    batch_size=8,
    num_epochs=20,
    learning_rate=0.005,
    val_split=0.2,
)
```

On a NAIP water-mapping dataset, this configuration reaches a best validation IoU around 0.97. Note the higher `learning_rate=0.005` here, a more aggressive setting that requires watching the training curves carefully to confirm the model is converging smoothly rather than overshooting, exactly the kind of check introduced two pages ago.

### D. What explains the differences

```{admonition} These are not a controlled experiment
:class: warning
The three IoU figures above (0.71, 0.90, 0.97) come from three *different* datasets and test sets, not the same locations run through three sensors side by side. Do not read them as strict proof that "NAIP beats Sentinel-2 beats RGB" in general. They are still informative about *why* more spectral information and higher resolution tend to help, but a fair, controlled comparison would need to hold the study area and evaluation set fixed while only changing the input bands.
```

Two separate factors are at play across these examples: additional spectral bands (particularly NIR and SWIR) give the model physically meaningful signal that plain RGB lacks, while higher spatial resolution reveals finer geometric detail regardless of band count. A real project should reason about which of these two factors, or both, actually matters for its specific target class before assuming "more bands" or "higher resolution" alone will fix a segmentation problem.

---

## 4. Common pitfalls

- **Mismatching band order between training and inference imagery.** If your training data has bands in one order and your inference imagery has them in another, the model will silently misinterpret the input; always confirm band order explicitly rather than assuming.
- **Treating IoU differences across different datasets as a controlled comparison.** As above, isolate one variable (bands, resolution, or architecture) at a time if you want a genuinely fair comparison for your own project.
- **Assuming more bands always help.** Extra bands only help if they carry information relevant to your target class; adding irrelevant bands mainly adds training cost without a matching accuracy benefit.
- **Ignoring resolution when comparing sensor options.** A sensor with fewer bands but much finer resolution can still outperform a more spectrally rich but coarser one, depending on how small your target features are.

---

## 5. Mini task

Your project needs to map small farm ponds (roughly 10 to 30 meters across) using either freely available Sentinel-2 imagery (6 usable bands, 10 to 20 meter resolution) or NAIP imagery (4 bands, 1 meter resolution, limited geographic coverage). Which would you choose, and why?

:::{dropdown} Sample solution
:class: note

For ponds only 10 to 30 meters across, NAIP's 1-meter resolution is likely decisive: at Sentinel-2's 10-to-20-meter pixel size, a 15-meter pond might span only one or two pixels, which is far too coarse for reliable boundary delineation. This assumes NAIP coverage is available for the study area, which is the main caveat, since NAIP is limited to the United States; if the study area falls outside NAIP's coverage, Sentinel-2 would be the only option despite its coarser resolution.
:::

---

## 6. Key takeaways

- Switching sensors or band counts in `geoai` mainly means changing `num_channels`; the rest of the training and inference pipeline stays the same.
- Additional spectral bands, especially NIR and SWIR for water, can substantially improve separability when the target class has a distinctive spectral signature.
- Higher spatial resolution reveals finer geometric detail independent of band count, and can matter more than spectral richness for small targets.
- Comparing results across different datasets and sensors is not the same as a controlled experiment; be cautious about attributing a performance difference to a single factor.

### Further reading

- TorchGeo, ["Earth Surface Water"](https://docs.torchgeo.org/en/stable/tutorials/earth_surface_water.html) tutorial — background on the Sentinel-2 dataset (Luo et al., 2021) used in the multispectral example above.
- OpenGeoAI, ["Water Detection"](https://opengeoai.org/examples/water_detection/) — a related worked example of water mapping with `geoai`.
