---
site:
  outline_maxdepth: 2
---

# When to use it

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Recognising when you need to compare two time points, not analyse a single image
</div>
<!-- markdownlint-enable MD033 -->

---

The landing page introduced change detection as comparing two real observations. This page surveys where it is typically applied, and, more importantly, the three different shapes its output can take, since choosing the wrong one adds unnecessary complexity or leaves your actual question unanswered.

---

## 1. Motivation

"Detect change" is not a single, well-defined task until you specify what kind of answer you need. Deciding this early shapes every choice later in the lesson, from which method to use to how you set a detection threshold.

---

## 2. Core idea

{term}`Change detection <Change Detection>` output generally takes one of three forms:

- **Binary**: changed versus unchanged, a single yes/no per pixel or region. Simplest to interpret and act on.
- **Categorical**: what *kind* of change occurred (for example, forest to bare soil, versus forest to built-up area), useful when different change types call for different responses.
- **Continuous**: a magnitude or intensity of change, useful when "how much" matters more than a threshold decision, or when you want to defer the threshold choice to later analysis.

Matching your project's actual question to the right output type from the start avoids over-building (a categorical model when binary would do) or under-delivering (a binary mask when you actually needed to know what changed into what).

---

## 3. Typical applications by output type

### A. Binary change

Building demolition, new construction footprints, flood extent, and wildfire scars are commonly framed as binary questions: did this area change, or not. This is the most common starting point and the focus of this lesson's main workflow.

### B. Categorical change

Land-use and land-cover transition mapping (forest converted to agriculture, agriculture converted to urban) needs a categorical answer, since "changed" alone does not tell a planner or analyst what actually happened. This typically requires combining change detection with classification at both time steps, which is part of why the ChangeStar workflow later in this lesson produces per-date building segmentation alongside its change map.

### C. Continuous change

Glacier retreat, coastline erosion, and vegetation degradation are often better captured as a continuous measure of change intensity rather than a hard yes/no, since the meaningful information is in the degree and spatial pattern of change, not a single threshold crossing. Change Vector Analysis, covered two pages from now, naturally produces this kind of continuous magnitude.

---

## 4. Python reactivation

No new Python here. This page is about scoping the question your workflow needs to answer, before the following pages get into specific methods and code.

---

## 5. Common pitfalls

- **Defaulting to binary output without checking whether your project actually needs more.** If different types of change call for different responses in your project, a purely binary mask may not be enough.
- **Choosing continuous output when a simple binary decision is all that is needed.** Continuous output adds interpretive work; do not take it on without a reason.
- **Assuming one output type answers every question in a project.** A single project might reasonably need a binary flood-extent mask for one question and a continuous erosion-rate estimate for another.

---

## 6. Mini task

Your project tracks informal settlement growth in a peri-urban area over five years. Which output type, binary, categorical, or continuous, best matches "how much informal settlement area was added, and where is growth concentrated"? Justify your choice.

:::{dropdown} Sample solution
:class: note

A continuous output (or, at minimum, a binary mask aggregated into an area/intensity measure) fits best, since the question is fundamentally about magnitude and spatial concentration of growth, not just a yes/no per location and not a multi-category classification of change type. A purely binary mask could still answer this if you separately compute the total changed area and its spatial distribution, but framing the underlying detection as intensity-aware from the start (as CVA does) keeps that information available without needing a second processing step.
:::

---

## 7. Key takeaways

- Change detection output takes one of three forms: binary (changed or not), categorical (what kind of change), or continuous (how much change).
- Matching output type to your actual project question avoids unnecessary complexity or an incomplete answer.
- Many real applications, like ChangeStar's building-change workflow later in this lesson, combine change detection with per-date classification to get closer to a categorical answer.
- The next page looks at what makes two images comparable in the first place, a prerequisite for any of these three output types to be trustworthy.

### Further reading

- Esri, ["How Change Detection Works"](https://developers.arcgis.com/python/latest/guide/how-change-detection-works/) — a platform-agnostic overview of change detection approaches and output types.
- Picterra, ["Change Detection"](https://picterra.ai/change-detection/) — a commercial platform's overview of common change detection applications.
