---
site:
  outline_maxdepth: 2
---

# Train a classifier

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Training a first ResNet-50 model with the GeoAI API
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this workflow matters

This page is the practical core of the lesson: a single function call takes you from a directory of labeled tiles to a trained, evaluated model. Understanding its parameters means you can adapt this workflow directly to your own project's data later.

---

## 2. Core idea

`train_image_classifier` handles the full pipeline in one call: it scans the {term}`ImageFolder <ImageFolder Format>` directory, performs a stratified train/validation/test split, builds the model, runs the training loop, and saves the best {term}`checkpoint <Checkpoint>`.

Training an image classifier means adjusting model parameters so that predicted labels become closer to reference labels in the {term}`Training Set`.

The practical workflow is:

```text
load labelled images
→ split into train, validation and test data
→ create model
→ train for several epochs
→ save checkpoint
→ inspect training history
```

The example uses ResNet-50 with ImageNet pre-training. This is a sensible first baseline for many classification tasks.

---

## 3. Workflow

### A. Import training functions

```{code-cell} python
import matplotlib.pyplot as plt

from geoai.recognize import (
    train_image_classifier,
    plot_training_history,
)

import os
from geoai.utils import download_file

url = "https://data.source.coop/opengeos/geoai/EuroSAT-RGB.zip"
data_dir = download_file(url)
```

### B. Train a ResNet-50 classifier

The `train_image_classifier()` function handles the main steps: scanning the directory, creating train/validation/test splits, constructing the model, running the training loop and saving the best checkpoint.

```{code-cell} python
result = train_image_classifier(
    data_dir=data_dir,
    model_name="resnet50",
    num_epochs=5,
    batch_size=32,
    learning_rate=1e-3,
    image_size=64,
    in_channels=3,
    pretrained=True,
    output_dir="image_recognition_output/resnet50",
    num_workers=4,
    seed=42,
)
```

Key parameters:

| Parameter | Meaning |
| --- | --- |
| `data_dir` | Root folder of the ImageFolder dataset. |
| `model_name` | Architecture name, here `resnet50`. |
| `num_epochs` | Number of full passes through the training data. |
| `batch_size` | Number of images processed together. |
| `learning_rate` | Step size used during optimisation. |
| `image_size` | Input image size expected by the model. |
| `in_channels` | Number of input channels; EuroSAT RGB has 3. |
| `pretrained` | Whether to start from pre-trained weights. |
| `output_dir` | Folder for logs and checkpoints. |
| `seed` | Random seed for reproducibility. |

```{tip}
If your dataset is small, or you want a faster first run, add `freeze_backbone=True` to keep the pre-trained convolutional layers fixed and train only the final classification layer, the "backbone freezing" strategy from the [previous lesson page](02_from-pixels-to-labels.md).
```

### C. Know what the result contains

`train_image_classifier` returns a dictionary with the trained `model`, the `class_names`, the `checkpoint_path`, and the already-split `train_dataset`, `val_dataset`, and `test_dataset` objects, everything the next page needs for evaluation.

### D. Plot training history

Training curves help you check whether the model is learning and whether it may be {term}`Overfitting`.

```{code-cell} python
fig = plot_training_history("image_recognition_output/resnet50/models")
plt.show()
```

Look for:

- training and validation accuracy increasing,
- training and validation loss decreasing,
- validation loss not diverging strongly from training loss,
- signs that more epochs may or may not help.

```{warning}
A good training curve does not guarantee a good spatial model. It only shows behaviour on the current dataset split. You still need test-set metrics and visual prediction checks.
```

---

## 4. Python reactivation

The dictionary that `train_image_classifier` returns is an ordinary Python dictionary, the same pattern you saw with `stats` in [L04's batch workflows](../L04/05_batch-workflows-and-pairing.md). Accessing `result["model"]` or `result["class_names"]` is exactly the same operation you have used throughout this course.

---

## 5. Common pitfalls

- **Mismatched `image_size` or `in_channels`.** These must match your actual data; a mismatch usually produces a clear error, but double-check them against the dataset you explored on the previous page.
- **Assuming 5 epochs always suffices.** Treat it as a starting point and adjust based on the training curves, not as a fixed rule for every dataset.
- **Skipping the `seed` argument.** Without a fixed seed, your train/validation/test split changes every run, which makes it impossible to compare experiments fairly, a problem the [comparison page](07_architecture-comparison-and-reuse.md) depends on you avoiding.

---

## 6. Mini task

Before running it, predict what would change if you swapped `model_name="resnet50"` for `model_name="efficientnet_b0"` while keeping every other argument identical. Then, if you have time, run it and check your prediction.

:::{note} Sample solution
:class: dropdown

Prediction: training should run somewhat faster per epoch, since EfficientNet-B0 has roughly a fifth the parameters of ResNet-50, and final accuracy should land in a similar range, since both architectures tend to perform comparably on EuroSAT after fine-tuning. Memory use during training should also be lower. Running it typically confirms this: similar accuracy, faster wall-clock training time.
:::

## Further experimenting

- [Image Recognition with EuroSAT Dataset (opengeoai.org)](https://opengeoai.org/examples/image_recognition/) — the official worked example this page's workflow is based on, useful as a reference for the full `geoai.recognize` API and its parameters.

---

## 7. Key takeaways

- `train_image_classifier` combines dataset splitting, model construction, training, and checkpointing in one call.
- Key {term}`hyperparameters <Hyperparameter>` are `model_name`, `pretrained`, `num_epochs`, `batch_size`, `learning_rate`, `image_size`, `in_channels`, `output_dir`, and `seed`.
- `freeze_backbone=True` offers a faster, lighter-weight alternative to full fine-tuning for small datasets.
- Training and validation curves, not a fixed epoch count, are the right way to judge whether training has converged or started overfitting.
