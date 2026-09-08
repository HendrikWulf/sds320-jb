---
site:
  outline_maxdepth: 2
---

# Deep learning approaches

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Deep learning architectures for comparing image pairs, such as Siamese networks
</div>
<!-- markdownlint-enable MD033 -->

---

The previous page's methods compared pixels one at a time, with no sense of the surrounding scene. This page explains, at a decision-oriented level, how deep learning models overcome that limitation, before the next page runs one such model, ChangeStar, on real imagery.

---

## 1. Motivation

`geoai`'s ChangeStar workflow, used for the rest of this lesson, is built on the architecture family introduced here. Understanding the general idea, comparing learned features rather than raw pixels, is what will make ChangeStar's behavior and outputs make sense two pages from now, rather than feeling like an unexplained black box.

---

## 2. Core idea

The dominant architecture for deep-learning change detection is the **{term}`siamese network <Siamese Network>`**: two parallel encoder branches, with identical, shared weights, process the two input images separately, extracting feature representations that capture spatial context, not just individual pixel values. A comparison module then identifies differences between the two feature maps at corresponding spatial locations. Because both branches share weights, they produce features in the same representation space, which is what makes a direct comparison between them meaningful in the first place.

---

## 3. How the comparison works

### A. Why spatial context helps

A pixel-wise method (previous page) cannot tell a genuinely changed rooftop from an isolated noisy pixel; both look the same to it. A siamese network's encoder branches extract features that incorporate surrounding context, edges, textures, and shapes nearby, which helps the model learn to distinguish a real, spatially coherent change from scattered radiometric noise, the exact limitation flagged at the end of the previous page.

### B. Weight sharing

Both encoder branches in a siamese network have identical parameters. This is a deliberate design choice: it guarantees that a feature vector at a given position in the pre-change image lives in the same representation space as the feature vector at the same position in the post-change image, so a direct comparison between them is meaningful rather than comparing two unrelated feature spaces.

### C. Comparing features

The comparison module varies across specific architectures. Some compute a straightforward difference between the two feature maps; FC-Siam-diff, for example, uses element-wise subtraction. Others concatenate the two feature maps and let subsequent layers learn how to compare them, as FC-Siam-conc does. More recent variants use {term}`transformer <Transformer>`-based attention to model long-range spatial dependencies between the two time steps; BIT (Binary change detection with a Transformer) is a named example of this approach. You do not need to memorize these specific architectures; the pattern to recognize is that the comparison step itself, not just the feature extraction, is a design choice that varies across models.

```{tip}
When reading about a new change detection architecture, ask two questions: how does it extract features from each image (usually a shared-weight encoder), and how does it compare them (subtraction, concatenation, or attention)? Most architectures in this space are variations on those two choices.
```

---

## 4. Python reactivation

No new Python here. The next page runs a specific, pre-trained implementation of these ideas; this page is conceptual preparation for interpreting what that model is doing internally.

---

## 5. Common pitfalls

- **Assuming deep learning always outperforms traditional methods.** It generally handles spatial context and complex patterns better, but it also needs representative training data (or, as the next page covers, effective pretraining) to do so reliably; the unsupervised methods from the previous page remain useful when labeled data is unavailable.
- **Treating architecture choice as more important than data quality.** A siamese network trained or pretrained on unrepresentative data will not automatically outperform a well-tuned traditional baseline on your specific imagery.
- **Forgetting that co-registration and comparability still matter.** A deep learning model can tolerate more noise than pixel-wise differencing, but it does not remove the need for the comparability checks from two pages ago.

---

## 6. Mini task

A classmate says, "deep learning change detection works by training on pairs of images and just learning to predict the difference image, so it is basically the same idea as image differencing." Identify what this description misses.

:::{dropdown} Sample solution
:class: note

It misses that a siamese network compares learned *feature representations*, which incorporate spatial context (nearby edges, shapes, texture patterns), not raw pixel values directly. Image differencing has no way to distinguish an isolated noisy pixel from a genuine, spatially coherent change, since it treats every pixel independently; a siamese network's shared-weight encoders are specifically designed to capture that surrounding context before any comparison happens, which is the actual source of its advantage, not simply "learning to predict a difference" in the pixel-arithmetic sense.
:::

---

## 7. Key takeaways

- Siamese networks process two images through weight-sharing encoder branches, then compare the resulting feature representations, not raw pixels.
- Weight sharing ensures both branches produce features in the same representation space, making direct comparison meaningful.
- Comparison modules vary (subtraction, concatenation, attention), but the underlying pattern, shared feature extraction plus a comparison step, is consistent across architectures.
- Deep learning approaches generally handle spatial context better than pixel-wise methods, but still depend on adequate training data or pretraining, and do not eliminate the need for comparable, well-aligned input imagery.

### Further reading

- TorchGeo, ["Change Detection"](https://docs.torchgeo.org/en/stable/tutorials/change_detection.html) tutorial — a different library's perspective on the same siamese-network family of approaches.
- [Awesome Remote Sensing Change Detection](https://github.com/wenhwu/awesome-remote-sensing-change-detection) — a curated list of change detection papers, datasets, and architectures, useful if you want to go deeper than this lesson's decision-oriented overview.
- Esri, ["Multi-Class Change Detection Using Segmentation Deep Learning Models"](https://developers.arcgis.com/python/latest/samples/multi-class-change-detection-using-segmentation-deep-learning-models/) — a worked example of categorical (multi-class) change detection on a different platform.
