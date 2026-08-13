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

A research question is the engine of your project. It guides what data you need, which method makes sense, how you evaluate your outputs and how you explain your results.

Without a clear question, a project can easily become a collection of tools: download data, run a model, make a map, write something about the output. With a clear question, the same tools become part of a focused spatial analysis.

In SDS320, your question should help you decide:

- what spatial phenomenon you study,
- where and when you study it,
- which data are suitable,
- which method fits,
- what output you expect,
- how you will judge whether the output is useful.

---

## From topic to question

It helps to distinguish five levels:

| Level | Meaning | Example pattern |
| --- | --- | --- |
| Topic | A broad area of interest | Urban heat, glacier change, tree crowns, flood damage |
| Problem | Why the topic matters | Heat exposure varies within cities and affects planning decisions |
| Research question | The question your project tries to answer | Where are high land-surface-temperature patterns most visible in a selected city? |
| Analytical task | What your workflow needs to do | Acquire imagery, preprocess data, calculate or model a spatial output, evaluate it |
| Expected output | What you will produce | Map, figure, metric, comparison, prediction or interpretation |

A topic is not yet a project. A project begins when you can connect a question to data, method, output and evaluation.

---

## What makes a good SDS320 research question?

A good SDS320 research question is usually:

- **spatial** — it asks about location, distribution, distance, area, change, objects or spatial relationships,
- **interesting or relevant** — it matters for an audience, field or applied problem,
- **feasible** — it can be answered within the available time, data and skills,
- **ethical** — it avoids harm and handles sensitive topics responsibly,
- **data-driven** — it can be addressed with accessible spatial data,
- **methodologically clear** — it suggests what kind of analysis is needed,
- **answerable within one semester** — it is not a multi-year research programme,
- **open enough** — it requires analysis, not only a yes/no answer,
- **narrow enough** — it can be implemented and communicated clearly.

The SDS110 project-planning material used the FINER criteria: feasible, interesting, novel, ethical and relevant. For SDS320, add one more practical test: can you imagine the main map, figure, metric or model output that would answer the question?

---

## Using PICOT for spatial questions

The PICOT framework can help you make a question more precise.

| PICOT element | Meaning for SDS320 |
| --- | --- |
| P — Population | Who or what is studied? This can be a place, object type, land-cover class, image set or spatial feature. |
| I — Intervention / Indicator | What is measured, detected, modelled or introduced? |
| C — Comparison | What is compared? This can be before/after, area A/area B, class A/class B or method A/method B. |
| O — Outcome | What result is measured or mapped? |
| T — Time | Over what period or observation date? |

Example structure:

```text
How does [indicator] differ between [comparison groups] for [population/features] in [area] during [time period]?
```

PICOT is not mandatory, but it helps you detect vague questions.

---

## Question patterns

Useful SDS320 question patterns include:

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

```text
How did [spatial pattern] change between [time 1] and [time 2]?
```

---

## Weak vs improved examples

The examples below are illustrative. Use them to understand the direction of improvement, not as fixed project topics.

| Weak question | Why it is weak | Improved question |
| --- | --- | --- |
| How has biodiversity changed? | Too broad and not clearly spatial or measurable. | How has bird species richness changed across selected Swiss alpine valleys between 1990 and 2020? |
| Can I use deep learning on satellite images? | Starts with a method, not a spatial problem. | Can semantic segmentation of Sentinel-2 imagery identify broad land-cover classes in a selected study area? |
| Which regions have worse flood problems? | Ambiguous terms and unclear metric. | How did mapped flood extent differ between selected municipalities during a defined flood event? |
| Has this glacier changed? | Too close to yes/no. | By how much did the glacier area change between two selected observation years, and where was retreat most visible? |
| Where are trees in Zurich? | Too broad unless data and method are clear. | Can high-resolution aerial imagery and object detection identify individual tree crowns in a selected Zurich neighbourhood? |
| Is the model good? | No task, metric or reference. | How accurately does the model classify selected land-cover classes compared with reference labels in the study area? |

---

## Scope check

Before settling on a question, complete this checklist:

- [ ] Study area: Is the area clearly defined and not too large?
- [ ] Time period: Is the time period clear and data available?
- [ ] Data source: Do you know where the data come from and how to access them?
- [ ] Data format: Can you open and inspect the data?
- [ ] Method: Does the method match the question and data?
- [ ] Expected output: Can you name the main map, figure, metric or table?
- [ ] Evaluation: Can you check whether the output is meaningful?
- [ ] Ethics: Could the project expose sensitive locations, people or communities?
- [ ] Feasibility: Can you complete a first prototype within the next few weeks?

If several boxes remain unclear, narrow the question before adding technical complexity.

---

## Decision points

Ask yourself:

1. Am I asking a spatial question or only using spatial data?
2. Does the question require analysis?
3. Can the question be answered with data I can access?
4. Is the method suitable for the data resolution and format?
5. What would count as a useful result?
6. What would count as a limitation?

---

## Mini task: improve your question

Write three versions:

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
- Use FINER and PICOT to make the question more precise.
- Avoid questions that are too broad, too simple or method-first.
- A feasible SDS320 question should connect to a realistic output and evaluation strategy.
