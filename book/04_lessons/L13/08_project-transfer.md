---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Deciding whether satellite embeddings fit your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

This lesson closes the course's method toolkit: registries, patch- and pixel-based embeddings, unsupervised exploration, lightweight classification, and cloud-based comparison. This page is not about learning anything new. It is about deciding, concretely, whether embeddings belong in your project, alongside or instead of the trained and prompted methods from every earlier lesson, and turning that decision into a concrete plan for the work ahead.

---

## 2. Project checklist

Work through this list for your own project:

- [ ] I can state my analysis goal clearly: similarity search, clustering, lightweight classification, or change comparison.
- [ ] I have checked the `geoai` embedding registry for a dataset covering my study area at a useful resolution and time range.
- [ ] I know whether patch-based or pixel-based embeddings fit my question better.
- [ ] If my project needs classification, I have identified a realistic source of even a small number of labeled points.
- [ ] I have a plan for visualizing or validating results (PCA, geographic mapping, or spot-checking), not just trusting a similarity score or cluster assignment.
- [ ] I have considered whether my project genuinely needs the precision of a task-specific trained model from an earlier lesson instead.
- [ ] I can state clearly what an embedding-based result in my project would, and would not, prove.

---

## 3. Decision points

**What is your actual analysis goal?** Revisit the framework from the first page of this lesson: similarity, grouping, or lightweight classification all fit embeddings well; precise boundaries and calibrated measurements do not.

**Patch-based, or pixel-based?** Scene-level questions (regional comparison, land-use classification at the scale of a tile) fit patch-based embeddings like Clay; fine-grained, per-pixel questions fit pixel-based embeddings like TESSERA or AlphaEarth.

**Local download, or cloud-based?** If you need to combine embeddings with other local data or run custom analysis, a downloadable format (Clay, TESSERA) fits better. If your question is a well-defined comparison over a large area, AlphaEarth's cloud-based approach avoids a potentially large local download.

**How will you get labels, if you need them?** Even a small number of confidently labeled points can support a lightweight classifier; identify a realistic source (your own knowledge of the study area, existing datasets, a quick manual labeling pass) before assuming this step will be easy.

**How will you validate results?** Decide upfront whether you will check cluster assignments geographically, spot-check similarity search results visually, or evaluate a classifier's per-class performance, not just its overall accuracy, the same lesson from earlier in this course applied here.

---

## 4. Common pitfalls

- **Treating a similarity score, cluster assignment, or classifier prediction as a finished answer without checking it.** This is the throughline of this entire lesson; embeddings accelerate exploration, they do not replace judgment.
- **Choosing embeddings for a question that actually needs precise geometry or calibrated measurement.** Revisit the decision framework from page 1 if you find yourself forcing an embedding-based answer to a question it is not suited for.
- **Assuming any embedding dataset covers your specific study area well.** Check the registry's spatial extent and resolution fields directly rather than assuming "global" coverage means locally reliable.
- **Reporting overall classifier accuracy without checking class balance.** As the classification page emphasized, an imbalanced label set can make a weak classifier look deceptively strong.

---

## 5. Mini deliverable

Produce a short embeddings project plan (half a page is enough) that states:

1. Your analysis goal (similarity, clustering, classification, or change comparison) and why embeddings fit it.
2. Your chosen embedding dataset (or a short list of candidates), including format (patch or pixel) and why it matches your study area and question.
3. Your label source, if your goal involves classification, and a realistic estimate of how many labeled points you can obtain.
4. Your validation plan: how you will check results before relying on them.
5. One thing an embedding-based result in your project would *not* prove, and how you will communicate that limit.

---

## 6. Reflection questions

1. Does your project's core question genuinely fit similarity, grouping, or lightweight classification, or does it need the precision of an earlier lesson's task-specific method?
2. If you used embeddings for part of your project and a trained model for another part, how would those two pieces fit together in your overall project narrative?
3. What would make you trust a cluster assignment or similarity result enough to include it in a project conclusion?
4. Looking back across this entire course, which combination of methods, from image recognition through embeddings, best matches what your project actually needs?
5. What is one interpretation limit of your chosen method that you will document explicitly, rather than leaving implicit, in your final project write-up?
