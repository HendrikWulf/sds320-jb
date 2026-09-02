---
site:
  outline_maxdepth: 2
---

# Pixels to labels

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Understanding how a classifier turns an image chip into a prediction
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

`train_image_classifier` on the [training page](05_training-a-classifier.md) will feel like a black box unless you understand, at least at a conceptual level, what happens inside it. Knowing the input-output logic also helps you debug: if a model performs oddly, you will have a better sense of whether the problem is in the features it is learning or in the labels you gave it.

---

## 2. Core idea

A classifier takes a fixed-size image and produces a probability distribution over a set of predefined classes; the class with the highest probability becomes the prediction. Getting there requires turning raw pixel values into a compact, meaningful representation first.

---

## 3. Workflow

### A. From raw pixels to a feature vector

A 64×64 RGB tile contains 64 × 64 × 3 = 12,288 individual pixel values. On their own, these numbers do not mean much; a model has to learn which patterns in them correspond to "forest" versus "highway" versus "residential." Earlier approaches computed hand-crafted features by hand, statistics like average brightness, texture measures, or spectral indices, and fed those into a traditional classifier such as a {term}`random forest <Random Forest>` or a support vector machine. Deep learning replaces this manual step with features the network learns directly from the data.

### B. How a CNN builds features

A {term}`convolutional neural network <Convolutional Neural Network (CNN)>` processes an image through layers of learned filters. Early layers tend to detect simple patterns like edges and color gradients; deeper layers combine these into textures, shapes, and eventually recognizable structures such as rooftops or tree canopies. A key property is weight sharing: the same filter is applied at every position in the image, so a filter that detects a road texture in one corner also detects it elsewhere, without needing separate parameters for every location. This also makes CNNs reasonably tolerant of the object simply appearing in a different part of the tile.

### C. From features to class probabilities

After the convolutional layers extract a feature representation, one or more final layers map those features onto a probability for each class. The predicted label is simply the class with the highest probability, which is also why you will see confidence scores alongside predictions later in this lesson.

Example:

```text
Forest: 0.82
Residential: 0.10
Pasture: 0.05
River: 0.03
```

The predicted label is usually the class with the highest probability.

### D. Transfer learning

Training a deep CNN from scratch needs far more labeled images than most remote sensing projects have available. {term}`Transfer learning <Transfer Learning>` starts instead from a model {term}`pre-trained <Pre-Trained Model>` on a large, general-purpose dataset, typically ImageNet, roughly 1.2 million natural photographs across 1,000 categories, and then {term}`fine-tunes <Fine-Tuning>` it on your target data. The pre-trained model has already learned broadly useful low-level features (edges, textures, color patterns); fine-tuning mainly adapts the higher-level features to your {term}`downstream task <Downstream Task>`.

Two common strategies:

- **Full fine-tuning**: every parameter updates during training. Gives the model the most flexibility, but needs more data to avoid {term}`overfitting <Overfitting>`.
- **Backbone freezing**: the pre-trained convolutional {term}`backbone <Backbone>` stays fixed, and only the final classification layer trains. Faster, and works well with small datasets, but limits how much the model can adapt.

| Strategy | What happens | Use when |
| --- | --- | --- |
| Full fine-tuning | All model parameters are updated. | You have enough labelled data and want the model to adapt fully. |
| Backbone freezing | The feature extractor is frozen and only the classification head is trained. | You have limited data or want faster experiments. |

```{admonition} Natural photos are not satellite images
:class: caution
ImageNet weights transfer surprisingly well to remote sensing imagery, because low-level features like edges and textures are fairly domain-agnostic. That transfer is not free of assumptions, though: a model pre-trained on street-level photos has never seen a top-down view, and features tuned for three visible-light channels may need real adaptation if you later work with additional spectral bands.
```

---

## 4. Common pitfalls

- **Assuming a classifier "sees" objects the way a person does.** It only sees statistical patterns in pixel values; a texture that happens to correlate with a class can fool it, even without resembling the class visually.
- **Treating ImageNet pre-training as a guarantee of good transfer.** It is a strong starting point, not a substitute for checking that your fine-tuned model actually performs well on your specific imagery.
- **Defaulting to full fine-tuning without considering backbone freezing.** For a small dataset, freezing the backbone can train faster and generalize better than trying to update every parameter.

---

## 5. Mini task

In two or three sentences, explain to a classmate who has not taken this course why a model pre-trained on photos of everyday objects can still be useful for classifying satellite image tiles.

:::{note} Sample solution
:class: dropdown

The early layers of a CNN mostly learn general-purpose visual building blocks, edges, color gradients, simple textures, regardless of what the training photos actually show. Those building blocks are useful for almost any image, including satellite tiles, so starting from a model that already learned them saves the network from having to relearn the basics from a small remote sensing dataset. Only the later layers, which combine those building blocks into task-specific patterns, need real adaptation to the new domain.
:::

---

## 6. Key takeaways

- A classifier maps pixel values to a probability distribution over classes; the highest-probability class is the prediction.
- CNNs learn features hierarchically, from edges to textures to recognizable structures, using shared filters across the image.
- Transfer learning starts from ImageNet-pretrained weights instead of training from scratch, which needs far less labeled data.
- Full fine-tuning adapts every parameter; backbone freezing adapts only the final layer and suits smaller datasets.

---

## 7. Further watching

- [But what is a neural network? (3Blue1Brown)](https://youtu.be/aircAruvnKk?si=SIDHnI8BFfXM-8ht) — a great explainer on how neural networks function. You can probably see the connection between numbers to land cover types.
