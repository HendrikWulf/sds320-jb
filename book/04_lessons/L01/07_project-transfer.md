---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning this lesson into progress on your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this page matters

Every idea in this lesson, GeoAI's definition, why spatial is special, the domains it is used in, the tools, the seven core tasks, and the traditional-to-foundation-model trajectory, exists to help you answer one question for your own project: what should your model actually predict, and with what kind of approach? This page does not introduce new theory. It asks you to commit, provisionally, to a first direction.

---

## 2. Project checklist

- [ ] Named a rough application domain (urban, agriculture, environment, disaster, water, health, or something outside these six)
- [ ] Written a one-sentence rough research question or decision your project would inform
- [ ] Identified a tentative core task type (one of the seven from [this page](05_core-geoai-tasks.md))
- [ ] Noted which one or two "why spatial is special" properties (from [this page](02_why-spatial-data-is-different.md)) will matter most for your data
- [ ] Made a first guess about whether a traditional method, a CNN, or a fine-tuned foundation model fits your likely labeled-data volume

---

## 3. Decision points

**Domain:** Pick something you are genuinely curious about, not just the first example from this lesson. A domain you find personally interesting is easier to stay motivated on through a full semester.

**Task type:** Use the three-question check from [Core GeoAI tasks](05_core-geoai-tasks.md): whole-image or located, boxes or pixels, category or number. If two task types both seem plausible, note both and let [L02 – Data acquisition](../02_data-acquisition.md) help you decide based on what data is actually available.

**Paradigm:** A reasonable default: if you expect to hand-label fewer than roughly 100 examples, lean toward a fine-tuned foundation model or a traditional method; if you expect several hundred or more, a CNN trained more specifically for your task becomes realistic within a semester.

**Scope:** A study area you can meaningfully cover in one semester, rather than "the whole country," is a reasonable choice at this early stage. You can always expand later if time allows.

---

## 4. Common pitfalls

- **A domain with no available data.** An idea that sounds compelling but depends on imagery, labels, or sensors you cannot access is not yet a feasible project. You will test this directly in L02.
- **A task type chosen before a question.** "I'll do semantic segmentation" is not a project until it is attached to a specific question a pixel-level map would actually answer.
- **Scope creep at the idea stage.** If your one-sentence research question already contains "and," consider whether it is really two projects.
- **No plan for evaluation.** If you cannot describe, even roughly, how you would know whether your model is any good, that is a sign the question needs sharpening before you commit further.

---

## 5. Mini deliverable

Write a short **Project Idea Sketch** (150–250 words) containing:

1. Your candidate domain and a one-sentence research question.
2. Your tentative core task type, and a one-sentence justification.
3. The one or two spatial-data properties (from [this lesson](02_why-spatial-data-is-different.md)) most likely to shape your data choices.
4. A first guess at data sources, even if unconfirmed, that you will check in L02.
5. A first guess at paradigm (traditional method, CNN, or fine-tuned foundation model) and why.

Save this sketch somewhere you will return to. You are not committing to it permanently, but it gives L02 something concrete to test against real data availability.

---

## 6. Reflection questions

- What would change about your project if the data you hoped for turns out not to be available at the resolution or coverage you need?
- If your task type guess turns out to be wrong, which of the seven core tasks is your most likely fallback, and why?
- Who would actually use the output of this project, and does your current one-sentence question give them something useful?
- Which single property of spatial data (from the earlier page) poses the biggest risk to your idea, and what would you need to check early to reduce that risk?
