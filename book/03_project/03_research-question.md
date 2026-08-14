---
site:
  outline_maxdepth: 2
---

# Research question

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning a broad spatial idea into a feasible project question
</div>
<!-- markdownlint-enable MD033 -->

---

## Why the research question matters

A research question is the engine of your project. It guides what data you need, which method makes sense, how you evaluate outputs and how you explain results.

Without a clear question, a project can become a loose collection of tools: download data, run a model, make a map and then search for a story. With a clear question, the tools become part of a focused spatial analysis.

---

## From topic to question

A topic is not yet a project. A project begins when you connect a question to data, method, output and evaluation.

| Level | Meaning | Example pattern |
| --- | --- | --- |
| Topic | Broad area of interest | Urban heat, glacier change, tree crowns, flood damage |
| Problem | Why the topic matters | Heat exposure varies within cities and affects planning decisions |
| Research question | What your project tries to answer | Where are high land-surface-temperature patterns most visible in a selected city? |
| Analytical task | What your workflow needs to do | Acquire imagery, preprocess data, calculate or model an output, evaluate it |
| Expected output | What you produce | Map, figure, metric, comparison, prediction or interpretation |

---

## What makes a good research question?

A good SDS320 research question is usually:

- **spatial** — it asks about location, distribution, distance, area, change, objects or spatial relationships,
- **feasible** — it fits the available time, data and skills,
- **ethical** — it avoids harm and handles sensitive topics responsibly,
- **data-driven** — it can be addressed with accessible spatial data,
- **methodologically clear** — it suggests what kind of analysis is needed,
- **narrow enough** — it can be implemented and communicated in one semester,
- **open enough** — it requires analysis, not only a yes/no answer.

The SDS110 project-planning material (lecture 4) used the FINER criteria: feasible, interesting, novel, ethical and relevant. For SDS320, add one practical test: can you imagine the main map, figure, metric or model output that would answer the question?

---

## Use PICOT to sharpen the question

The PICOT framework can help you make a question more precise.

| PICOT element | Meaning for SDS320 |
| --- | --- |
| P — Population | Who or what is studied? This can be a place, object type, land-cover class, image set or spatial feature. |
| I — Intervention / Indicator | What is measured, detected, modelled or introduced? |
| C — Comparison | What is compared? For example before/after, area A/area B, class A/class B or method A/method B. |
| O — Outcome | What result is measured or mapped? |
| T — Time | Over what period or observation date? |

PICOT is not mandatory, but it helps reveal vague questions.

```text
How does [indicator] differ between [comparison groups] for [population/features] in [area] during [time period]?
```

---

## Useful question patterns

```text
How does [spatial phenomenon] vary across [area/time/classes]?
```

```text
Can [data source or method] identify [feature/change/pattern] in [study area]?
```

```text
To what extent does [variable] relate to [spatial outcome]?
```

```text
Where are [features/events/changes] most visible in [data]?
```

```text
How well can [method] map or predict [spatial feature] from [input data]?
```

---

## Weak vs improved examples

The examples below are illustrative.

| Weak question | Why it is weak | Improved question |
| --- | --- | --- |
| How has biodiversity changed? | Too broad and not clearly spatial or measurable. | How has bird species richness changed across selected Swiss alpine valleys between 1990 and 2020? |
| Can I use deep learning on satellite images? | Starts with a method, not a spatial problem. | Can semantic segmentation of Sentinel-2 imagery identify broad land-cover classes in a selected study area? |
| Which regions have worse flood problems? | Ambiguous terms and unclear metric. | How did mapped flood extent differ between selected municipalities during a defined flood event? |
| Has this glacier changed? | Too close to yes/no. | By how much did glacier area change between two selected observation years, and where was retreat most visible? |
| Is the model good? | No task, metric or reference. | How accurately does the model classify selected land-cover classes compared with reference labels in the study area? |

---

## Scope check

Before settling on a question, check:

- [ ] Is the study area clearly defined?
- [ ] Is the time period clear?
- [ ] Can you access and inspect the data?
- [ ] Does the method fit the data format and resolution?
- [ ] Can you name the main output?
- [ ] Can you evaluate or at least check the output?
- [ ] Are there ethical or privacy risks?
- [ ] Can you build a first prototype soon?

If several answers are unclear, narrow the question before adding technical complexity.

---

## Decision points

Ask yourself:

1. Am I asking a spatial question or only using spatial data?
2. Does the question require analysis?
3. Can the question be answered with data I can access?
4. What would count as a useful result?
5. What would count as a limitation?

---

## Mini task

Write three versions to improve your question:

```text
Broad topic:
Draft question:
Improved SDS320 research question:
```

Then complete:

```text
My project asks ...
It uses ...
It compares or measures ...
It produces ...
It can be evaluated by ...
```

---

## Key takeaways

- A good research question guides data, methods, workflow, evaluation and communication.
- Start broad, then narrow.
- FINER and PICOT can help make the question more precise.
- Avoid questions that are too broad, too simple or method-first.
- A feasible question connects to a realistic output and evaluation strategy.
