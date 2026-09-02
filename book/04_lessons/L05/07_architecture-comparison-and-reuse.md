---
site:
  outline_maxdepth: 2
---

# Compare and reuse

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Comparing classifiers fairly and sharing a trained model
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

"Model A scored higher than Model B" is only a meaningful statement if everything else was held constant. Otherwise, you cannot tell whether the architecture caused the difference, or something else did, a different random split, a different number of epochs, or a different batch size.

---

## 2. Core idea

A fair architecture comparison changes exactly one thing, the architecture, and holds the data split, number of epochs, batch size, image size, and random seed identical across runs.

---

## 3. Comparison workflow

:::{note} Previous code
:class: dropdown

This page assumes that `result` is the trained ResNet-50 output from the previous page.

```{code-cell} python
# Import training functions and data

from geoai.recognize import (
    train_image_classifier,
    evaluate_classifier,
    predict_images,
    plot_confusion_matrix,
    plot_predictions,
)
from geoai.utils import download_file$
import os
import random
import matplotlib.pyplot as plt

url = "https://data.source.coop/opengeos/geoai/EuroSAT-RGB.zip"
data_dir = download_file(url)

```

```{code-cell} python
# Train a ResNet-50 classifier

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

```{code-cell} python
# Evaluate the ResNet-50 on the test set

eval_result = evaluate_classifier(
    model=result["model"],
    dataset=result["test_dataset"],
    class_names=result["class_names"],
)
```

:::

### A. Train EfficientNet-B0

EfficientNet-B0 is a lighter architecture than ResNet-50. Use the same settings as the ResNet-50 run and change only `model_name` and `output_dir`.

```{code-cell} python
result_effnet = train_image_classifier(
    data_dir=data_dir,
    model_name="efficientnet_b0",
    num_epochs=5,
    batch_size=32,
    learning_rate=1e-3,
    image_size=64,
    in_channels=3,
    pretrained=True,
    output_dir="image_recognition_output/efficientnet_b0",
    num_workers=4,
    seed=42,
)
```

### B. Plot training history

```{code-cell} python
# Compare training curves side by side
fig = plot_training_history("image_recognition_output/efficientnet_b0/models")
plt.show()
```

Comparing this against the ResNet-50 curves from the [training page](05_training-a-classifier.md) shows how quickly each architecture converges, and whether either shows signs of overfitting.

### C. Evaluate EfficientNet-B0

```{code-cell} python
eval_effnet = evaluate_classifier(
    model=result_effnet["model"],
    dataset=result_effnet["test_dataset"],
    class_names=result_effnet["class_names"],
)

print(f"ResNet-50 accuracy:       {eval_result['accuracy']:.4f}")
print(f"EfficientNet-B0 accuracy: {eval_effnet['accuracy']:.4f}")
```

| Model | Parameters | Typical EuroSAT accuracy | Relative speed |
| --- | --- | --- | --- |
| ResNet-50 | ~25M | ~93–97% | Baseline |
| EfficientNet-B0 | ~4M | ~93–97% | Faster |

Your exact numbers will vary with the random split, training duration, and hardware. For most geospatial classification tasks, the two architectures perform similarly once fine-tuned on a well-curated dataset. A model that scores one or two percentage points lower in accuracy can still be the better practical choice if it is several times smaller, trains faster, or fits on more limited hardware.

### D. Plot the normalised confusion matrix

```{code-cell} python
fig = plot_confusion_matrix(
    eval_effnet["confusion_matrix"],
    result_effnet["class_names"],
    normalize=True,
)
plt.show()
```

Compare this matrix with the ResNet-50 matrix. Focus on class confusions, not only the overall score.

### E. Compare overall accuracy

```{code-cell} python
print(f"ResNet-50 accuracy: {eval_result['accuracy']:.4f}")
print(f"EfficientNet-B0 accuracy: {eval_effnet['accuracy']:.4f}")
```

A slightly lower accuracy may still be acceptable if the model is smaller, faster or easier to run with your hardware.

### F. Record the comparison

```{code-cell} python
comparison = [
    {
        "model": "resnet50",
        "accuracy": eval_result["accuracy"],
        "epochs": 5,
        "batch_size": 32,
        "learning_rate": 1e-3,
    },
    {
        "model": "efficientnet_b0",
        "accuracy": eval_effnet["accuracy"],
        "epochs": 5,
        "batch_size": 32,
        "learning_rate": 1e-3,
    },
]

comparison
```

This simple record is useful for your project report and repository.

---

## 4. Optional: publish and reuse

Sharing a trained model lets collaborators run inference immediately, without your original training data or compute. `push_classifier_to_hub` uploads the best checkpoint's weights and a small configuration file to a versioned repository; it needs a free Hugging Face account and a write-access token.

This is optional for SDS320. Use it only if model sharing is useful for your project and you are comfortable managing access tokens.

```{warning}
Do not commit Hugging Face tokens, API keys or credentials to your repository.
```

```{code-cell} python
from huggingface_hub import notebook_login
notebook_login()
```

```{code-cell} python
from geoai.recognize import push_classifier_to_hub, predict_images_from_hub

repo_url = push_classifier_to_hub(
    model_path=result["checkpoint_path"],
    repo_id="your-username/eurosat-resnet50",
    model_name="resnet50",
    num_classes=len(result["class_names"]),
    in_channels=3,
    class_names=result["class_names"],
    commit_message="EuroSAT ResNet-50 classifier trained for 5 epochs",
)
print(repo_url)
```

`predict_images_from_hub` downloads the model back down and runs inference, without needing the local checkpoint at all, which makes it easy to hand a working classifier to anyone with the repository ID.

```{code-cell} python
hub_result = predict_images_from_hub(
    image_paths=test_paths[:10],
    repo_id="your-username/eurosat-resnet50",
    image_size=64,
)
```

---

## 5. Common pitfalls

- **Changing more than one variable between comparison runs.** If epochs, batch size, or the random seed differ between two runs, you can no longer attribute a performance difference to architecture alone.
- **Judging architectures on accuracy alone.** Training time, model size, and inference speed often matter as much for a real deployment or a compute-limited student project.
- **Treating Hugging Face Hub sharing as required.** It is a convenience for collaboration and reuse, not a core part of the SDS320 workflow.

---

## 6. Mini task

Sketch a fair comparison plan for two architectures on your own project: which single variable would you change, and which five or six variables would you deliberately hold fixed?

:::{note} Sample solution
:class: dropdown

Variable changed: `model_name` (`resnet50` vs. `efficientnet_b0`). Held fixed: data directory and split, `num_epochs`, `batch_size`, `learning_rate`, `image_size`, `in_channels`, and `seed`. Documenting this list explicitly, before running either experiment, makes it much easier to trust and explain the comparison later in a project report.
:::

---

## 7. Further reading

- [OpenGeoAI: Image Recognition with EuroSAT Dataset](https://opengeoai.org/examples/image_recognition/)
- [IBM: What is image classification?](https://www.ibm.com/think/topics/image-classification)

---

## 8. Key takeaways

- A fair comparison changes only the architecture and holds every other setting, including the random seed, identical.
- ResNet-50 and EfficientNet-B0 often perform similarly on a well-curated dataset like EuroSAT; the practical difference is usually speed and size, not accuracy.
- Judge architecture trade-offs on the full picture: accuracy, training time, model size, and inference speed.
- Publishing a model to a hub like Hugging Face is a useful, optional way to share or reuse a trained classifier, not a required course workflow.
