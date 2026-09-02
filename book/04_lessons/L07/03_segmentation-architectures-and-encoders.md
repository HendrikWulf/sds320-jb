---
site:
  outline_maxdepth: 2
---

# Architectures & encoders

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
The encoder-decoder pattern behind nearly every segmentation model
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

You now know what a segmentation model predicts. This page looks at how it gets there, and gives you enough of a decision framework to pick a reasonable architecture and encoder for the workflow on the next page.

The `geoai` package, through the `segmentation_models.pytorch` library, exposes architecture and encoder as two independent string parameters. Like the architecture choice in L06, switching between them is nearly free in code. The real work is understanding what each choice trades off, so you can make a deliberate decision for your own project data rather than copying a default without knowing why.

---

## 2. Core idea

Every segmentation network separates two concerns. The **architecture** is the overall blueprint: how data flows through the network and how the final pixel-level output is produced. Within that blueprint, an {term}`encoder-decoder architecture <Encoder-Decoder Architecture>` is the standard pattern: an encoder progressively compresses the input image into compact features, and a decoder reconstructs those features back to the original spatial resolution, producing the final per-pixel class map.

---

## 3. Building blocks

### A. Encoder

The **encoder** compresses an input image into a smaller, information-rich representation, filtering out noise while keeping the patterns that matter. This is the same role the {term}`backbone <Backbone>` played in L06's detection architectures, and in practice, the encoders you will choose from (ResNet variants, EfficientNet variants) are the same families.

Conceptually:

```text
high-resolution image
        ↓
edges and textures
        ↓
larger patterns
        ↓
compact feature representation
```

As spatial dimensions become smaller, the model can learn increasingly abstract patterns.

### B. Decoder

The **{term}`decoder <Decoder>`** takes the encoder's compressed representation and reconstructs it back to the input's original height and width, producing the final segmentation map.

```text
compact features
        ↓
upsampling
        ↓
spatial reconstruction
        ↓
pixel-level prediction
```

The result is a class prediction for each pixel.

### C. Skip connections

Without any further help, a decoder reconstructing purely from the most compressed, "bottleneck" representation would lose fine spatial detail: precise edges, corners, and narrow features. **{term}`Skip connections <Skip Connection>`** solve this by forwarding the encoder's higher-resolution feature maps directly to the decoder at matching spatial scales, bypassing the bottleneck. The decoder then combines this spatial detail with the semantically rich features it has reconstructed from deeper layers, which is how a model can correctly classify a pixel as "building" while also placing the building edge in exactly the right location.

```{admonition} U-Net is named for a reason
:class: note
{term}`U-Net`'s distinctive "U" shape in architecture diagrams comes from the encoder path going down, the decoder path going back up, and skip connections crossing directly between matching levels on each side. Once you can picture that shape, most encoder-decoder segmentation diagrams become easier to read.
```

### D. Choosing an architecture

For most geospatial segmentation tasks, U-Net with a pre-trained {term}`ResNet` encoder is a strong, well-tested starting point, and it is the architecture used throughout the workflow pages in this lesson. A few alternatives are worth knowing about for specific situations:

| Architecture | Key idea | When it may help |
| --- | --- | --- |
| `unet` | Symmetric encoder-decoder with skip connections at every level | Fine boundary delineation; the default starting point |
| `deeplabv3plus` | {term}`DeepLabV3+` uses dilated convolutions to capture context at multiple scales without shrinking the feature map as much | Scenes needing broader context alongside boundary detail |
| `fpn` | Aggregates a {term}`Feature Pyramid Network (FPN)` across multiple encoder levels | Classes that vary widely in spatial scale within the same scene |
| `pspnet` | {term}`PSPNet` pools features at multiple region sizes for global scene context | Scenes where broad context (a whole land-cover pattern) matters more than fine edges |
| `segformer`, `dpt` | {term}`Transformer`-based encoders and decoders | Experimentation once you have outgrown CNN-based baselines; generally need more data and compute |

```{tip}
Start with `unet`. Only move to an alternative once you have a specific, diagnosed reason, for example a class that consistently loses its boundary detail, or a scene that needs more global context than U-Net captures. Switching architectures without a specific problem to solve rarely pays off.
```

### E. Choosing an encoder

Common encoder choices include `resnet34`, `resnet50`, `efficientnet-b0`, and `mobilenet_v2`. Nearly any encoder can be paired with nearly any architecture in `segmentation_models.pytorch`, which is what makes the two-parameter design (`architecture`, `encoder_name`) convenient to experiment with.

The real power of these encoders comes from {term}`transfer learning <Transfer Learning>`: rather than training an encoder from scratch on a small geospatial dataset, you start from an encoder already pre-trained on millions of natural images, typically ImageNet. It has already learned to recognize edges, textures, and shapes, patterns that transfer reasonably well to remote sensing imagery. {term}`Fine-tuning <Fine-Tuning>` this pre-trained encoder on your own labeled data is faster, needs less data, and typically outperforms training from scratch.

`resnet34` is a reasonable default: deep enough to learn useful spectral and textural patterns, while remaining fast enough for the kind of rapid iteration a course project needs.

### F. Input bands

ImageNet pre-training is based on three-channel imagery. Remote-sensing imagery may contain:

- 3 channels: RGB (e.g. SWISSIMAGE),
- 4 channels: RGB + NIR (e.g. NAIP),
- 6 or more multispectral bands (e.g. Sentinel-2).

In the provided `geoai` workflow, `num_channels` tells the model how many image bands to expect.

```{important}
A model must receive the same number of channels, in the same order and with compatible preprocessing, during training and inference.
```

---

## 4. Common pitfalls

- **Choosing an exotic architecture before trying the default.** U-Net with a ResNet encoder solves most geospatial segmentation problems adequately; reach for alternatives only after diagnosing a specific limitation.
- **Assuming a bigger encoder is automatically better.** A larger encoder (`resnet50` versus `resnet34`) needs more data and compute to train well; with a small project dataset, it can perform worse, not better.
- **Forgetting that skip connections are what preserve boundary detail.** If your project cares specifically about precise edges (property boundaries, small structures), an architecture that skimps on skip connections is a poor fit, whatever else it offers.
- **Changing architecture and encoder at the same time when troubleshooting.** As with L06, vary one factor at a time so you know what actually caused a change in results.

---

## 5. Mini task

Your project needs to map wetlands, which have highly irregular, gradual boundaries rather than sharp edges, across a large study area with limited labeled data. Would you start with `unet` and a `resnet34` encoder, or would you consider an alternative? Justify your answer in two or three sentences.

:::{dropdown} Sample solution
:class: note

`unet` with `resnet34` is still a reasonable starting point, since wetlands do have some boundary structure worth preserving through skip connections, and `resnet34` suits a limited-data setting well through transfer learning. If initial results show the model struggling with wetlands' broader spatial context (for example, confusing small isolated wet patches with surrounding vegetation), `pspnet` would be a reasonable next experiment, since its global pooling is designed for exactly this kind of broad-context scene understanding, but it is a second step, not a first one, given the limited data available.
:::

---

## 6. Key takeaways

- Every modern segmentation architecture separates an encoder (compresses input into features) from a decoder (reconstructs features into a pixel-level output); skip connections link them to preserve spatial detail.
- U-Net with a pre-trained ResNet encoder is a strong, well-tested default for most geospatial segmentation tasks.
- DeepLabV3+, FPN, and PSPNet trade some of U-Net's fine boundary detail for different kinds of multi-scale or global context; choose them to solve a specific, diagnosed problem, not by default.
- Transfer learning from ImageNet-pre-trained encoders is what makes segmentation practical on the modest labeled datasets typical of a course project.

### Further reading

- `segmentation_models.pytorch` [documentation](https://smp.readthedocs.io/en/latest/encoders.html) — the full list of supported architectures and encoders underlying `geoai`'s segmentation functions.
- Ultralytics, ["Semantic Segmentation"](https://docs.ultralytics.com/tasks/semantic) — a general (non-geospatial) overview of the same task from a different library's perspective, useful for comparison.
