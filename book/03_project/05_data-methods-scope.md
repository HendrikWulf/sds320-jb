---
site:
  outline_maxdepth: 2
---

# Data, methods and scope

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Keeping your project feasible and methodologically coherent
</div>
<!-- markdownlint-enable MD033 -->

---

## Why alignment matters

A good SDS320 project does not simply combine an interesting dataset with an impressive method. The research question, available data and analytical method need to fit together.

A project can fail even when each part sounds good individually. A strong GeoAI method will not help if the data do not contain the target feature. A detailed dataset will not help if the question is too broad. A good research question will not work if the data cannot be accessed, processed or evaluated in time.

This page helps you check project fit before the workflow becomes too complex.

---

## The alignment triangle

Think of your project as a triangle:

```text
Research question
↔ Available data
↔ Analytical method
```

Each side needs to support the others.

```{mermaid}
flowchart TD
    Q[Research question] --- D[Available data]
    D --- M[Analytical method]
    M --- Q
    Q --> O[Meaningful output]
    D --> O
    M --> O
```

A coherent project can complete this sentence:

```text
My project asks [question], uses [data], applies [method], and produces [output].
```

---

## Choosing suitable data

When choosing data, check the following criteria.

| Criterion | Questions to ask |
| --- | --- |
| Spatial resolution | Are features visible at the scale of the data? |
| Temporal coverage | Does the dataset cover the relevant date, season or time period? |
| Study area | Does the dataset cover the full {term}`Area of Interest (AOI)`? |
| Data format | Can you open, process and document the format? |
| Licence | Are you allowed to use and share the data or derived outputs? |
| Accessibility | Can you download or access the data reliably? |
| Size | Can you process the data with your available time and hardware? |
| Quality | Are there clouds, gaps, noise, missing values or uncertain labels? |
| Documentation | Are metadata, methods and limitations described clearly? |

For GeoAI projects, data suitability also depends on the task. For example, object detection needs objects that are visible and separable in the imagery. Segmentation needs labels or masks if you train or evaluate a model. Change detection needs comparable observations across time.

---

## Choosing suitable methods

Choose methods based on the question and data.

Use this logic:

| Project need | Possible method direction |
| --- | --- |
| Classify full images or scenes | Image recognition or classification |
| Locate discrete objects | Object detection |
| Assign a class to every pixel | Semantic segmentation |
| Separate individual objects | Instance segmentation |
| Compare places or times | Change detection or spatial comparison |
| Predict continuous values | Regression |
| Explore similarity or representation space | Satellite embeddings |
| Use a broad pre-trained model | Foundation model or prompt-based workflow |

This table is a starting point, not a rule. The final method choice should be justified in your report and presentation.

```{tip}
A simpler baseline method can strengthen your project. It gives you something to compare with and helps you understand whether a more complex method adds value.
```

---

## Managing scope

Scope is the size and complexity of your project. Managing scope does not mean making the project weak. It means making it possible to finish well.

Ways to reduce scope include:

- smaller study area,
- shorter time period,
- fewer classes,
- fewer input datasets,
- fewer model comparisons,
- one clear output,
- lower spatial resolution,
- one representative case study,
- simpler baseline method,
- less automation for optional steps.

For a semester project, depth is usually better than width. A focused project with a clear output, evaluation and limitation discussion is stronger than a broad project that remains unfinished.

---

## Data-method fit examples

These examples are illustrative.

| Question type | Data need | Method fit |
| --- | --- | --- |
| Where are individual trees visible in a neighbourhood? | High-resolution imagery and object annotations or validation data | Object detection or instance segmentation |
| How did land cover change between two dates? | Comparable imagery from both dates | Change detection or classification comparison |
| Can imagery separate water, vegetation and built-up areas? | Multispectral imagery and reference labels or plausibility checks | Semantic segmentation or classification |
| Where are prediction errors largest? | Model output and reference data | Error map, residual analysis or uncertainty discussion |
| Which image areas look similar? | Image chips or embeddings | Satellite embeddings and clustering or similarity search |

---

## Red flags

| Red flag | First check |
| --- | --- |
| The research question does not mention where or when | Add study area and time period. |
| The method is chosen before the question is clear | Return to [Research question](03_research-question.md). |
| The data are interesting but do not answer the question | Revise the question or find better data. |
| The target feature is not visible in the imagery | Use different data, reduce expectations or change the task. |
| The project needs labels but none are available | Create a small labelled dataset, use a pre-trained model or change scope. |
| The dataset is too large to process | Reduce study area, time period, resolution or number of files. |
| There is no evaluation strategy | Identify reference data, visual checks or meaningful comparison. |
| The project has many outputs but no main result | Choose one key output and build the workflow around it. |

---

## Decision points

Before implementation, decide:

- What is the smallest version of the project that can answer the question?
- Which dataset is essential, and which is optional?
- Which method is necessary, and which is only interesting?
- What output will show whether the project worked?
- How will you discuss uncertainty or limitations?
- What will you simplify if the first prototype fails?

---

## Mini task: alignment statement

Write one alignment statement for your project:

```text
My project asks ...
It uses ...
It applies ...
It produces ...
It will be evaluated by ...
The main limitation is likely ...
```

Then check whether every part fits together. If one part feels weak, revise it before adding more complexity.

---

## Key takeaways

- Question, data and method need to fit together.
- Interesting data and impressive methods are not enough on their own.
- Scope reduction is a normal project skill.
- A focused project is easier to implement, evaluate and communicate.
- Choose methods because they answer the question, not because they sound advanced.
