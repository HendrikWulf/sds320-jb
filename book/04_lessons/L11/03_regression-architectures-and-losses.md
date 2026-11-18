---
site:
  outline_maxdepth: 2
---

# Regression architectures

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Encoder-decoder architectures with a regression head, and losses like mean squared error
</div>
<!-- markdownlint-enable MD033 -->

---

The previous page established what changes conceptually between classification and regression. This page gets specific: which architectures work for regression, which {term}`loss function <Loss Function>` to choose, and how to keep predictions within a valid range, before the next pages prepare data and train a real model.

---

## 1. Motivation

You already know U-Net and its relatives from L07. That knowledge transfers almost directly: the architectural shift from segmentation to regression is a single changed layer. The decisions that matter more for a regression result are the loss function and how you handle the valid range of your target, and both have details that are easy to get wrong.

---

## 2. Core idea

Encoder-decoder architectures built for segmentation, {term}`U-Net`, UNet++, {term}`DeepLabV3+`, and {term}`Feature Pyramid Network (FPN) <Feature Pyramid Network>`, all work for regression. The encoder still extracts multi-scale features, the decoder still reconstructs spatial detail, and {term}`skip connections <Skip Connection>` still preserve the fine-grained detail needed for precise per-pixel output. The change is concentrated in the output head and the loss function.

---

## 3. What actually changes

### A. Reusing the encoder-decoder pattern

Everything you learned about the {term}`encoder-decoder architecture <Encoder-Decoder Architecture>` in L07 still applies: the encoder compresses the input into features, the decoder reconstructs them back to full spatial resolution, and skip connections keep the fine spatial detail that the compressed bottleneck alone would lose. Precise per-pixel values need the same spatial precision as segmentation boundaries, which is why skip connections matter just as much here.

| Choice | Options in `geoai` | Starting point in this lesson |
| --- | --- | --- |
| `architecture` | `"unet"`, `"unetplusplus"`, `"deeplabv3plus"`, `"fpn"`, `"linknet"`, `"manet"`, `"pan"`, … | `"unet"` |
| `encoder_name` | Any encoder of `segmentation_models_pytorch`, e.g. `"resnet18"`, `"resnet34"`, `"resnet50"`, `"efficientnet-b0"` | `"resnet34"` |
| `encoder_weights` | `"imagenet"` or `None` | `"imagenet"` |
| `loss_type` | `"mse"`, `"l1"`, `"huber"` | depends on the target (section C) |

### B. The output head

A segmentation model ends in N output channels (one per class) followed by softmax. A regression model ends in a {term}`regression head <Regression Head>` with a single output channel and no activation: the number it produces is the prediction. `geoai.train_pixel_regressor()` sets this up for you (internally `classes=1` and no activation).

A consequence: the network itself can output any number, including negative heights or an {term}`NDVI <Normalized Difference Vegetation Index>` of 1.3. Some models add a {abbr}`ReLU (rectified linear unit, an activation that sets negative values to zero)` at the end to forbid negative values; `geoai` does not. Instead, you enforce the valid range in two places (section D).

### C. Choosing a loss function

Three loss functions cover most regression use cases:

| Loss | `loss_type` | Penalty for an error *e* | Large errors (outliers) | Typical use |
| --- | --- | --- | --- | --- |
| {term}`Mean squared error <Mean Squared Error>` (MSE) | `"mse"` | *e*² | Dominate the loss | Clean reference data; large errors are especially bad |
| {term}`Mean absolute error <Mean Absolute Error>` (MAE, L1) | `"l1"` | \|*e*\| | Count proportionally | Noisy reference data; every error equally important |
| {term}`Huber loss <Huber Loss>` (Smooth L1) | `"huber"` | ½ *e*² for small errors, \|*e*\| − ½ for large ones | Count proportionally | A compromise: smooth near zero, robust to outliers |

What "small" and "large" mean for Huber loss depends on a threshold. `geoai` uses PyTorch's `SmoothL1Loss` with its default threshold of **1, measured in the units of your target**. The figure shows what this means for the two targets of this lesson:

```{figure} images/loss_functions.png
:alt: Two panels comparing MSE, MAE and Huber loss as a function of prediction error. Left, for errors between −0.6 and 0.6 NDVI, the Huber curve is exactly half the MSE curve. Right, for errors between −8 and 8 metres, the Huber curve runs parallel to the MAE line except within ±1 m.
:width: 100%

The same three loss functions for errors in NDVI units (left) and in metres of tree height (right). With NDVI, errors are always smaller than 1, so Huber loss is exactly half the MSE. With tree heights, most errors are larger than 1 m, so Huber loss behaves almost like MAE.
```

So for NDVI, `"huber"` and `"mse"` train towards the same result; only the size of the loss values differs. For tree heights in metres, `"huber"` is close to `"l1"`. This is a good example of why it pays to know what a library does with a parameter, rather than relying on the general description of a method.

```{tip}
MSE minimises the squared error, which is exactly what {term}`root mean squared error <Root Mean Squared Error>` (RMSE) measures, so it is a good default whenever RMSE is the score you will report. Switch to Huber or MAE when your reference data contain gross errors (a wrong lab value, a misregistered plot) that should not dominate training. Measure, do not assume: in this lesson MSE gave the lowest errors both for NDVI (page 5) and for tree heights from stereo matching (page 8).
```

### D. Valid ranges and clipping

Many regression targets have a known physical range: NDVI lies between −1 and 1, heights and biomass cannot be negative. `geoai` enforces this in two places:

1. **During tile creation** (page 4): `target_min` and `target_max` in `create_regression_tiles()` clip target values to the range, and skip tiles in which more than 5% of the target values are outside it.
2. **During prediction** (page 6): `clip_range` in `predict_raster()` clips the predicted values.

The first keeps invalid reference values out of training; the second catches the occasional implausible prediction. Neither changes the model itself.

---

## 4. Common pitfalls

- **Defaulting to MSE without considering your data's noise level.** If your reference measurements contain occasional large errors, MSE lets those few pixels dominate training; MAE or Huber reduce their influence.
- **Assuming Huber loss behaves the same for every target.** Its threshold is in target units; for a target with small values such as NDVI it is identical to (half) MSE.
- **Relying on the network to respect a physical range.** The regression head has no activation; enforce valid ranges with `target_min`/`target_max` and `clip_range`.
- **Spending most of your time on architecture choices.** For many regression problems, the input bands, the loss function and the quality of the reference data affect results more than the specific encoder-decoder architecture.

---

## 5. Mini task

Your project predicts soil organic carbon content (in percent, non-negative) from imagery paired with soil-sample lab measurements that are known to include occasional measurement errors. Typical values lie between 1 and 8%. Which `loss_type` would you start with? How would Huber loss behave for this target? How would you prevent negative predictions?

:::{dropdown} Sample solution
:class: note

MAE (`"l1"`) or Huber are reasonable starting choices, because the occasional erroneous lab measurement would dominate an MSE loss. With `geoai`'s threshold of 1, Huber loss would be quadratic for errors below 1 percentage point and linear above, a sensible compromise for values between 1 and 8%. If the values were stored as fractions (0.01 to 0.08) instead, every error would be far below 1, and Huber would behave exactly like half the MSE, so the units of the target matter.

`geoai`'s regression head has no activation, so negative predictions are possible. Set `target_min=0` when creating tiles and `clip_range=(0, upper_limit)` when predicting.
:::

---

## 6. Key takeaways

- Segmentation architectures (U-Net, UNet++, DeepLabV3+, FPN) transfer to regression; only the output head changes to a single channel without activation.
- MSE, MAE and Huber loss treat large errors differently; in `geoai`, Huber's threshold is 1 in target units, so its behaviour depends on the scale of your target.
- Valid ranges are not built into the network; enforce them during tile creation (`target_min`, `target_max`) and prediction (`clip_range`).

### Further reading

- [segmentation_models_pytorch documentation](https://smp.readthedocs.io/) — the library behind `geoai`'s regression models, with all available architectures and encoders.
- PyTorch, [`SmoothL1Loss`](https://docs.pytorch.org/docs/stable/generated/torch.nn.SmoothL1Loss.html) — the exact definition of the Huber-type loss used by `loss_type="huber"`.
- OpenGeoAI, [regression module documentation](https://opengeoai.org/timm_regress/) — reference documentation for the regression functions used in this lesson.
