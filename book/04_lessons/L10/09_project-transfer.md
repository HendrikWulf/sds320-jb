---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Deciding whether change detection fits your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

This lesson covered a lot: output types, comparability challenges, two traditional baselines, siamese networks, and a full ChangeStar workflow with tuning and export. This page is not about learning anything new. It is about deciding, concretely, whether change detection fits your SDS320 project, and sketching the specific choices, dates, method, threshold, and validation plan, your project would need.

---

## 2. Project checklist

Work through this list for your own project:

- [ ] I can state what kind of change my project needs to detect, and which output type (binary, categorical, or continuous) fits that question.
- [ ] I have identified two (or more) candidate dates, and checked whether they are close enough in season to be genuinely comparable.
- [ ] I have a plan to check co-registration and general comparability before running any method.
- [ ] I have decided whether a traditional baseline (image differencing or CVA) would already answer my question, or whether I need a deep-learning approach.
- [ ] If using a pretrained model like ChangeStar, I have considered whether it was designed for my target change type (its focus on building change may not transfer to, for example, vegetation loss).
- [ ] I have a threshold strategy, and a reason for it, rather than defaulting to 0.5 without consideration.
- [ ] I have a plan for validating results, even informally, before trusting them for a project conclusion.

---

## 3. Decision points

**What output type does your project actually need?** Revisit the framework from the first page of this lesson. This decision shapes everything that follows.

**Which dates, and how far apart?** Match your date selection to your change type: abrupt changes can use closely spaced dates; gradual changes may need a longer time series, or at least dates far enough apart for the change to be detectable above noise.

**Traditional baseline, or deep learning?** If labeled training data is unavailable, or your project needs a fast, interpretable first look, start with image differencing or CVA. If your target change is subtle, spatially complex, or needs the kind of per-date understanding ChangeStar provides, a deep-learning approach is more appropriate, provided a suitable pretrained model or sufficient training data exists for your specific target.

**What threshold, and why?** As with confidence thresholds throughout this course, decide explicitly whether missed changes or false alarms are more costly for your application, and set your threshold accordingly, documenting the reasoning.

**How will you validate results without exhaustive ground truth?** Options include spot-checking a sample of detected changes against high-resolution basemap imagery, comparing traditional and deep-learning results for agreement, or checking detections against any known changes in your study area.

---

## 4. Common pitfalls

- **Choosing a change type that your available imagery cannot actually resolve.** Revisit the mixed-pixel and resolution discussion from earlier in this lesson before committing to a target.
- **Comparing dates from different seasons without a plan to handle phenological variation.** This remains one of the most common sources of unreliable results.
- **Applying a pretrained model like ChangeStar to a change type it was not designed for.** Building-focused pretraining does not automatically generalize to, for example, agricultural or vegetation change; check applicability before trusting results.
- **Treating a single threshold or model variant as final without comparison.** As the previous page demonstrated, both choices can meaningfully affect results; test before committing.
- **Skipping validation entirely because ground truth is expensive.** Even an informal spot-check against basemap imagery is better than no check at all.

---

## 5. Mini deliverable

Produce a short change-detection project plan (half a page is enough) that states:

1. Your target change type and the output format (binary, categorical, or continuous) your project needs.
2. Your candidate comparison dates, and how you will address any comparability concerns (season, co-registration, resolution).
3. Your planned method: traditional baseline, deep learning, or both, with a one-sentence justification.
4. Your threshold strategy and the reasoning behind it.
5. Your validation plan, even if informal.

---

## 6. Reflection questions

1. Does your project's change type call for a bitemporal comparison, or would a longer, multitemporal time series serve it better?
2. If your two candidate dates are not from the same season, how will you account for phenological variation?
3. Would a traditional baseline already answer your project's question, or does the added complexity of a deep-learning method genuinely pay off here?
4. If you used a pretrained model like ChangeStar, how would you check whether it generalizes to your specific target change and geographic setting?
5. How will you communicate the threshold trade-off (sensitivity versus specificity) to someone reading your project who was not involved in choosing it?
