---
site:
  outline_maxdepth: 2
---
# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning this lesson into concrete progress on your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

This lesson introduced image recognition as a tile-level classification workflow. You framed the task, inspected ImageFolder data, trained a classifier, evaluated it and compared architectures.

For your own project, the key decision is whether one label per image chip is useful enough. If it is, image recognition can be a clear and manageable modelling workflow. If it is not, the next lessons on object detection and segmentation may fit better.

After this page, you should be able to explain:

```text
what one chip represents
→ what classes are predicted
→ how training data are organised
→ how performance is evaluated
→ how the output supports the project question
```

---

## 2. Project checklist

- [ ] Decided whether image recognition genuinely fits your project question, using the check from [When This Task Fits](L05/01_when-to-use-image-recognition.md)
- [ ] Defined a candidate class list, with a rough sense of how balanced the classes are likely to be
- [ ] Sketched an ImageFolder structure for your data, and identified whether any L04 tiles can be reused
- [ ] Chosen a baseline architecture, with a one-sentence reason
- [ ] Written a short evaluation plan naming which metrics matter most for your question
- [ ] Named at least one class pair you expect to be confused, and why

---

## 3. Decision points

**Does the task genuinely fit?** Revisit the self-check from [page 1](L05/01_when-to-use-image-recognition.md). If your honest answer involves needing object locations or pixel-level detail, image recognition should be a first pass or a pre-filter, not your final method.

**How many classes, and how balanced?** Fewer, more visually distinct classes are easier to get a strong baseline on. If you expect severe imbalance, note it now; you will need it for your evaluation plan.

**Which architecture to start with?** Unless you have a specific reason not to, ResNet-50 is a reasonable default, per the [architecture page](L05/03_classification-architectures.md). Save architecture comparison for after you have a working baseline.

**What does "good enough" look like for your project?** Decide, before training, whether overall accuracy is the metric that matters most, or whether recall on one specific class (for example, correctly catching every flooded tile, even at the cost of some false alarms) matters more for your actual question.

---

## 4. Common pitfalls

- **Defining classes only after looking at confusing results.** Define them from your project question first; adjust only if the data genuinely does not support the distinction.
- **No evaluation plan beyond "check the accuracy."** A single number will not tell you whether the model fails in a way that matters for your specific question.
- **Ignoring class imbalance until training is already done.** If you can already tell one class will be rare, plan for it (more data collection, class weighting, or a metric other than raw accuracy) before you invest time in training.
- **Treating this as your final method without testing whether it truly answers your question.** A working classifier is a good sign, but only useful progress if what it predicts is what your project actually needs to know.

---

## 5. Mini deliverable

Produce a short **Image Recognition Plan** for your project:

1. Your candidate class list, and a one-sentence justification for each class.
2. Your planned ImageFolder structure, noting any L04 tiles you can reuse or relabel.
3. Your baseline architecture choice and why.
4. Your evaluation plan: which metric(s) matter most, and why.
5. At least one predicted class confusion, and what about the classes would cause it.

If your data is ready, also train a first baseline classifier and report its test accuracy and one thing you noticed in its confusion matrix.

---

## 6. Reflection questions

- Does your class list, once written down, still feel like it genuinely answers your project's question, or does it feel like a simplification you are not fully comfortable with?
- If your baseline model's accuracy turns out disappointing, is the more likely cause your data (too little, too imbalanced, mislabeled) or your task framing (maybe this is not actually a one-label-per-tile problem)?
- Which metric would you show first in a project report to a non-technical reader, and why?
- If your project eventually needs object locations or pixel-level detail, how would you use this lesson's classifier as a pre-filtering step for that heavier task?
