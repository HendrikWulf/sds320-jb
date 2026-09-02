---
site:
  outline_maxdepth: 2
---

# Foundation models

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
How the field evolved, and what that evolution means for how much data your project actually needs
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

Every paradigm shift in this field lowered the amount of domain expertise and labeled data required to build something useful. Knowing where a method sits on that trajectory tells you, roughly, how much labeling work you are signing up for.

---

## 2. Core idea

The field moved from hand-crafted features, to features learned end-to-end from raw imagery, to general-purpose representations that transfer across tasks with comparatively little fine-tuning. Each shift traded some interpretability and control for less labeling effort.

---

## 3. The evolution of paradigms

### A. Traditional machine learning

{term}`Random forests <Random Forest>`, support vector machines ({abbr}`SVM (Support Vector Machine)`), and gradient boosting dominated through the 2000s and early 2010s. They require hand-crafted features (spectral indices, texture measures) and work well on tabular or small-scale image data. They remain a reasonable choice when labeled data is scarce, compute is limited, or interpretability matters, since a random forest's feature importances are much easier to explain in a project report than a neural network's internal weights.

### B. Convolutional neural networks

Starting around 2015, architectures like {term}`U-Net` learned features directly from raw imagery, removing the need for manual feature engineering and improving performance on segmentation and detection substantially. The trade-off: {term}`CNN <Convolutional Neural Network (CNN)>`-based methods typically need large labeled datasets and task-specific training. Benchmark datasets and the availability of {term}`GPUs <GPU>` made this practical at scale.

### C. Foundation models

{term}`Foundation models <Foundation Model>` are large models pre-trained on massive, diverse datasets that learn general-purpose representations. {term}`SAM <Segment Anything Model (SAM)>`, trained on roughly a billion masks, can segment nearly any object without task-specific training. Geospatial-specific foundation models exist too, pre-trained on large volumes of satellite imagery and adaptable to tasks from flood mapping to crop classification with far less labeled data than training from scratch would need. `<!-- TODO: add current course-approved examples and links once a stable list of geospatial foundation models is confirmed for this term -->`

### D. Vision-language models

Models such as {term}`CLIP` connect visual understanding with natural language, matching images to text descriptions. This enables zero-shot classification: finding images that match a plain-language description ("show me deforestation") without any labeled training examples for that specific query. This lowers the barrier further, since you no longer need to curate a labeled dataset before you can start exploring a question.

### E. AI agents

The newest, still-emerging layer combines multiple models and tools with reasoning steps to carry out a multi-step workflow autonomously. An {term}`AI agent` given a request like "map buildings damaged by the earthquake in this region" could, in principle, chain together imagery retrieval, {term}`change detection <Change Detection>`, building {term}`segmentation <Semantic Segmentation>`, and damage classification without a person manually running each step.

```{admonition} What this means for your project timeline
:class: important
Fine-tuning a foundation model or prompting SAM directly usually gets you a usable result faster than training a CNN from scratch, provided the model was pre-trained on data reasonably similar to yours. If you have very little labeled data and a tight semester timeline, that trade-off is worth taking seriously.
```

---

## 4. Python reactivation

No code here either. The relevant reactivation is conceptual: think of "fine-tuning" the way you already think of reusing a function with different arguments, you are not rewriting the whole model, only adapting part of it to your data. You will see this pattern concretely with {term}`fine-tuning <Fine-Tuning>` and {term}`transfer learning <Transfer Learning>` in later lessons.

---

## 5. Common pitfalls

- **Assuming a foundation model will work well on your data with zero adaptation.** Zero-shot and lightly fine-tuned performance both still depend on how similar your imagery is to what the model saw during pre-training.
- **Choosing a foundation model purely because it is the newest option.** If your labeled dataset is already large and your task is narrow, a CNN trained specifically for it can still outperform a general-purpose model.
- **Treating interpretability as unimportant.** If your project's argument depends on explaining *why* the model predicted something, a traditional method with clear feature importances may serve your report better than a large opaque model.

---

## 6. Mini task

Imagine a project with very little labeled data: perhaps 40 hand-labeled examples. Would you start with traditional machine learning and hand-crafted features, a CNN trained from scratch, or a fine-tuned foundation model? Justify your choice in two to three sentences, considering labeled-data volume, compute, and interpretability.

:::{note} Sample solution
:class: dropdown

With only 40 labeled examples, training a CNN from scratch is unlikely to work well; CNNs generally need far more labeled data to learn useful features on their own. A fine-tuned foundation model is the stronger starting point, since it already carries general-purpose representations learned from a much larger pre-training dataset and typically needs only a small amount of task-specific data to adapt. Traditional machine learning with hand-crafted features remains a reasonable fallback if interpretability is a priority for the final report, or if a fine-tuned foundation model is not available for the specific sensor or region involved.
:::

---

## 7. Key takeaways

- The field moved from hand-crafted features, to CNNs that learn features directly, to foundation models that transfer across tasks, to emerging AI agents that orchestrate multi-step workflows.
- Each shift reduced the labeled-data requirement, at some cost to interpretability and control.
- Traditional machine learning remains a valid choice when data is scarce, compute is limited, or interpretability is a priority.
- Matching a method's paradigm to your project's realistic data volume and timeline is a project-scoping decision, not just a technical preference.
