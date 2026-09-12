---
site:
  outline_maxdepth: 2
---

# Regression architectures

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Encoder-decoder architectures with a regression head, and losses like MSE
</div>
<!-- markdownlint-enable MD033 -->

---

The previous page established what changes conceptually between classification and regression. This page gets specific: which architectures actually work for regression, and which loss function to choose, before the next two pages prepare data and train a real model.

---

## 1. Motivation

You already know U-Net and its relatives from L07. The good news is that this knowledge transfers almost directly; the architectural shift from segmentation to regression is smaller than you might expect, which means most of your existing intuition still applies here.

---

## 2. Core idea

Encoder-decoder architectures built for segmentation, {term}`U-Net`, UNet++, {term}`DeepLabV3+`, and {term}`Feature Pyramid Network (FPN) <Feature Pyramid Network>`, all work for regression with minimal modification. The encoder still extracts multi-scale features, the decoder still reconstructs spatial detail, and {term}`skip connections <Skip Connection>` still preserve the fine-grained detail needed for precise per-pixel output. The real change is concentrated in the output head and the loss function, not the backbone itself.

---

## 3. What actually changes

### A. Reusing the encoder-decoder pattern

Everything you learned about the {term}`encoder-decoder architecture <Encoder-Decoder Architecture>` pattern in L07 still applies directly: the encoder compresses the input into features, the decoder reconstructs them back to full spatial resolution, and skip connections keep the fine spatial detail that a heavily compressed bottleneck alone would lose. Precise per-pixel regression values need exactly the same spatial precision segmentation boundaries did, which is why skip connections matter just as much here.

### B. The output head

A segmentation model ends in N output channels (one per class) followed by softmax. A regression model ends in a single output channel, with no activation function, or a ReLU activation when the target is known to be non-negative, such as height or biomass, where ReLU simply prevents the model from predicting an impossible negative value. `geoai` handles this switch automatically: setting up a regression task configures the underlying architecture with a single output channel rather than requiring you to modify the architecture yourself.

### C. Choosing a loss function

Three loss functions cover most regression use cases:

- **{term}`MSE (Mean Squared Error) <Mean Squared Error>`** averages the squared differences between predicted and actual values. Squaring makes it sensitive to outliers, penalizing large errors heavily, useful when minimizing worst-case error matters most.
- **{term}`MAE (Mean Absolute Error) <Mean Absolute Error>`** averages the absolute differences instead. It is more robust to outliers than MSE, since it does not amplify large errors through squaring, though its gradient stays constant regardless of error size, which can slow convergence as training approaches the optimum.
- **{term}`Huber loss <Huber Loss>`** combines both: it behaves like MSE for small errors (smooth gradients near zero) and like MAE for large errors (reduced sensitivity to outliers). It is a reasonable default when your reference data may contain noise or occasional extreme values, which is common with field-measured or LiDAR-derived targets.

`geoai` exposes all three through a `loss_type` parameter (`"mse"`, `"l1"`, `"huber"`), so switching between them is a one-line change, the same low-cost experimentation pattern you have seen for architecture and encoder choices in earlier lessons.

```{tip}
If you are not sure which loss to start with, Huber is a reasonable default for real-world geospatial reference data, which is rarely perfectly clean. Switch to MSE if worst-case errors matter most for your application, or to MAE if you want every error treated proportionally regardless of size.
```

### D. Valid ranges and clipping

Many regression targets have a known physical range: NDVI falls between −1 and 1, heights and biomass cannot be negative. You can enforce this domain knowledge in two places: filtering target values during tile creation, and clipping predicted values during inference. Both are covered in more detail on the data-preparation and inference pages ahead; the important idea here is that combining both, rather than relying on either alone, produces the cleanest results, since the model learns approximate bounds from clean training data while inference-time clipping catches the occasional out-of-range prediction at the edges.

---

## 4. Python reactivation

No new Python syntax here. This page is a decision-making guide for two parameters, `architecture`/`encoder_name` and `loss_type`, you will set directly when calling `geoai.train_pixel_regressor()` two pages from now.

---

## 5. Common pitfalls

- **Defaulting to MSE without considering your data's noise level.** If your reference measurements are likely to include occasional outliers or errors, MAE or Huber may serve you better.
- **Forgetting to set a ReLU (or equivalent) for a non-negative target.** Without it, a model predicting height or biomass could technically output a negative value, which is physically meaningless.
- **Assuming architecture choice matters more than loss function for a given target.** For many regression problems, the loss function and valid-range handling affect results more than which specific encoder-decoder architecture you pick.
- **Ignoring valid-range enforcement entirely.** Skipping both tile-time filtering and inference-time clipping leaves your model exposed to noisy training data and implausible predictions alike.

---

## 6. Mini task

Your project predicts soil organic carbon content, a non-negative quantity, from imagery paired with soil-sample lab measurements that are known to include occasional measurement errors. Which loss function would you start with, and what output activation would you use? Justify both choices.

:::{dropdown} Sample solution
:class: note

Huber loss is a reasonable starting choice, since it behaves like MSE for the (presumably) majority of well-behaved measurements while reducing sensitivity to the occasional labeling error, exactly the noisy-reference-data situation Huber is suited for. For the output activation, a ReLU makes sense, since soil organic carbon content cannot be negative, and ReLU enforces that constraint directly in the model's output rather than relying on post-hoc clipping alone.
:::

---

## 7. Key takeaways

- Segmentation architectures (U-Net, UNet++, DeepLabV3+, FPN) transfer to regression with minimal change; skip connections still matter for spatial precision.
- The output head changes to a single channel, with no activation or a ReLU for non-negative targets.
- MSE, MAE, and Huber loss trade off outlier sensitivity differently; Huber is a reasonable default for noisy real-world reference data.
- Combining valid-range filtering at tile creation with output clipping at inference produces cleaner results than relying on either alone.

### Further reading

- [timm documentation](https://timm.fast.ai/) — reference for the encoder architectures selectable through `encoder_name`, the same library referenced in the course glossary's `timm` entry.
- OpenGeoAI, [timm regression module documentation](https://opengeoai.org/timm_regress/) — reference documentation for the underlying regression functions used in this lesson.
