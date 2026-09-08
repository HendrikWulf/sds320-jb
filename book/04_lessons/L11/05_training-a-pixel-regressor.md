---
site:
  outline_maxdepth: 2
---

# Training a pixel regressor

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A practical, end-to-end workflow for training a pixel regressor
</div>
<!-- markdownlint-enable MD033 -->

---

On the previous page, `train_imgs`, `train_tgts`, `val_imgs`, and `val_tgts` were prepared and split. This page uses them directly to train a regression model.

---

## 1. Motivation

This is the page where the architecture and loss-function decisions from two pages ago become an actual trained model. As with the training functions in earlier lessons, `geoai` hides most of the implementation behind a manageable set of parameters; your job is to understand what each one controls well enough to make good choices for your own project data later.

---

## 2. Core idea

`geoai.train_pixel_regressor()` builds the model, datasets, data loaders, and training loop in a single call, using an AdamW optimizer with a learning-rate scheduler that reduces the rate when validation loss plateaus, and early stopping that halts training if validation loss stops improving.

---

## 3. Workflow

### A. Train the model

```{code-cell} python
model = geoai.train_pixel_regressor(
    train_image_paths=train_imgs,
    train_target_paths=train_tgts,
    val_image_paths=val_imgs,
    val_target_paths=val_tgts,
    encoder_name="resnet34",
    architecture="unet",
    in_channels=in_channels,
    output_dir="ndvi_model",
    batch_size=8,
    num_epochs=100,
    learning_rate=1e-3,
    loss_type="mse",
    patience=20,
    verbose=False,
)
```

`architecture="unet"` and `encoder_name="resnet34"` select the encoder-decoder combination from two pages ago, using the same {term}`timm`-based, string-parameter selection pattern you have now seen across several lessons. `in_channels=in_channels` reuses the band count you read directly from the raster on the previous page, rather than hardcoding it, so the model automatically matches your actual input data.

### B. Understanding training parameters

`loss_type="mse"` selects the loss function from two pages ago; switching to `"l1"` or `"huber"` is a one-word change if you want to compare. `patience=20` controls early stopping: if validation loss has not improved for 20 consecutive epochs, training halts automatically, even if `num_epochs=100` has not been reached yet, which avoids wasting time training a model that has already stopped improving. The best checkpoint, not necessarily the one from the final epoch, is saved and returned automatically, the same "best, not just last" checkpoint principle from L07's segmentation training.

### C. Monitor the training history

```{code-cell} python
fig, history_df = geoai.plot_training_history(
    log_dir="ndvi_model",
    metrics=["loss", "r2"],
)
```

This plots training and validation loss alongside {term}`R-squared <R-squared (R²)>` over the training run. A healthy run shows both loss curves decreasing together, with R-squared climbing toward 1.0 as training progresses.

### D. Reading the curves

```{admonition} The same overfitting signal, in regression terms
:class: important
If training loss keeps dropping while validation loss starts rising, the model is overfitting, the same warning sign you learned to watch for in L07, just measured with a regression loss instead of a segmentation one. Watching R-squared alongside loss gives you a second, more interpretable confirmation: a validation R-squared that plateaus or declines while training R-squared keeps climbing tells the same story.
```

---

## 4. Python reactivation

`train_pixel_regressor()` is called entirely with keyword arguments, the same self-documenting pattern used for training functions throughout this course. Passing `in_channels=in_channels` (reusing a variable you computed earlier from the raster's actual band count) rather than typing a literal number is a small but useful habit: it keeps your code correct automatically if you later swap in imagery with a different number of bands.

---

## 5. Common pitfalls

- **Hardcoding `in_channels` instead of reading it from the data.** If your imagery's band count ever changes, a hardcoded value will silently mismatch it; reusing the value read directly from the raster, as in step A, avoids this.
- **Setting `patience` too low.** Training may stop before the model has genuinely converged, mistaking a temporary plateau for the end of useful learning.
- **Setting `patience` too high.** Training runs longer than necessary once the model has already stopped improving, wasting compute time without a corresponding benefit.
- **Checking only the loss curve, not R-squared.** The two together give a more complete picture than either alone; a loss value's absolute scale can be hard to interpret without R-squared's more intuitive 0-to-1 framing.

---

## 6. Mini task

Training stops automatically at epoch 61 out of a possible 100, with `patience=20`. What does this tell you about validation loss over the last 20 epochs before stopping, without looking at the plot directly?

:::{dropdown} Sample solution
:class: note

Stopping at epoch 61 with `patience=20` means validation loss did not improve at all between roughly epoch 41 and epoch 61, twenty consecutive epochs without a new best value, which is what triggered early stopping. This does not necessarily mean the model got worse during that window, only that it stopped making measurable progress on the validation set, which is exactly the situation early stopping is designed to catch efficiently.
:::

---

## 7. Key takeaways

- `train_pixel_regressor()` handles model construction, data loading, and the training loop in one call, using AdamW with a plateau-based learning-rate scheduler.
- `architecture` and `encoder_name` reuse the same architecture and loss decisions from the previous two pages; `in_channels` should be read from your actual data, not hardcoded.
- `patience` controls early stopping, halting training once validation loss stops improving, saving time without sacrificing the best checkpoint.
- Read loss and R-squared together; a widening gap between training and validation performance in either signals overfitting.

### Further reading

- OpenGeoAI, ["Train timm Regressor"](https://opengeoai.org/examples/train_timm_regressor/) — the full, runnable notebook this workflow is based on.
