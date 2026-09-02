---
site:
  outline_maxdepth: 2
---

# Architectures

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Choosing a sensible starting model for image recognition
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

You do not need to memorize {term}`architecture <Model Architecture>` internals to use them well. You do need enough intuition to make a reasonable first choice, and to know when it is worth trying an alternative. This page gives you that decision layer.

---

## 2. Core idea

Most geospatial classification tasks are well served by a small set of proven architecture families, differing mainly in parameter count, training speed, and how much data they need to perform well.

---

## 3. Workflow

### A. ResNet: the reliable baseline

{term}`ResNet` introduced skip connections that let gradients flow directly through the network, which made much deeper architectures trainable. Instead of learning a full mapping from input to output, each block only has to learn the difference between its input and the desired output, which makes optimization easier. ResNet-50, with roughly 25 million parameters, is the most common variant and a dependable default for remote sensing classification: it trains efficiently, generalizes well, and has a large body of published results to compare against.

### B. EfficientNet: fewer parameters

{term}`EfficientNet` scales network depth, width, and input resolution together using a single coefficient, rather than adjusting them independently. EfficientNet-B0, with about 4 million parameters, roughly a fifth the size of ResNet-50, often reaches comparable accuracy while training faster and using less memory. Larger variants (B3, B4, and beyond) trade some of that efficiency back for additional accuracy when your compute budget allows it.

### C. Vision Transformers and ConvNeXt

{term}`Vision Transformers <Vision Transformer (ViT)>` split an image into fixed-size patches and process them with self-attention, which lets any patch directly relate to any other patch, capturing long-range structure that a CNN would need many stacked layers to approximate. The trade-off is that transformers typically need more training data to match CNN performance, and their computation grows quadratically with the number of patches. ConvNeXt takes a different route to a similar destination: it keeps the convolutional structure of a CNN but borrows design choices from transformers, larger kernels, fewer activation functions, layer normalization, and can match or exceed Vision Transformer accuracy while keeping CNN-level efficiency.

### D. Picking one in practice

The `geoai` package supports over a thousand architectures through the {term}`timm` library, specified as a single string. That means trying an alternative architecture is a one-line change, not a rewrite. As a starting rule of thumb: ResNet-50 for a reliable, well-validated default; EfficientNet-B0 when training speed or deployment size matters; ConvNeXt or a Vision Transformer once your labeled dataset reaches into the tens of thousands of tiles.

```{admonition} Architecture choice is rarely the biggest lever
:class: tip
For most SDS320 projects, data quality and dataset size will affect your results more than the specific architecture you pick. Start with ResNet-50, get an end-to-end pipeline working, and only spend time comparing architectures once that baseline runs cleanly.
```

---

## 4. Common pitfalls

- **Assuming a bigger model is automatically better.** A Vision Transformer trained on a small dataset can underperform a ResNet-50 trained on the same data.
- **Comparing architectures without holding other settings fixed.** Architecture choice only means something if everything else (data split, epochs, batch size, seed) stays the same, a point the [comparison page](07_architecture-comparison-and-reuse.md) covers in more depth.
- **Treating parameter count as the only cost that matters.** Training time, memory use, and inference speed at deployment can matter as much as raw accuracy for a real project.

---

## 5. Mini task

Match each scenario to the architecture family that fits best, and briefly justify your choice.

1. You have 2,000 labeled tiles and want a dependable first result.
2. You need the trained model to run on a low-power edge device.
3. You have 80,000 labeled tiles and want to squeeze out extra accuracy.

:::{note} Sample solution
:class: dropdown

1. ResNet-50: a reliable, well-validated default that does not need a huge dataset to perform reasonably.
2. EfficientNet-B0: far fewer parameters than ResNet-50, which usually means faster, lighter inference.
3. ConvNeXt or a Vision Transformer: with a dataset this large, both are positioned to make good use of the extra data for additional accuracy.
:::

---

## 6. Key takeaways

- ResNet-50 is a reliable, well-validated default for geospatial image classification.
- EfficientNet-B0 offers comparable accuracy with far fewer parameters, useful when speed or deployment size matters.
- Vision Transformers and ConvNeXt can outperform CNNs on larger datasets but generally need more data to do so.
- Because `geoai` selects architectures through `timm` by name, comparing architectures is a one-line change, not a rewrite.
