---
site:
  outline_maxdepth: 2
---

# Project lifecycle

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
From first idea to final spatial analytics project
</div>
<!-- markdownlint-enable MD033 -->

---

## Why a project lifecycle helps

Spatial data science projects become easier to manage when you see them as a sequence of decisions and revisions.

At the start, your project may feel like a broad idea: urban heat, land-cover change, flood risk, vegetation monitoring, object detection or satellite embeddings. A project lifecycle helps you turn that idea into a concrete workflow with data, methods, outputs and interpretation.

The lifecycle also reminds you that project progress is not only “working code”. Progress can mean a clearer research question, a rejected dataset, a smaller study area, a better folder structure, a documented limitation or a figure that reveals a problem.

---

## The SDS320 project lifecycle

A useful SDS320 project lifecycle looks like this:

```text
Explore topic
→ formulate question
→ check data
→ choose method
→ design workflow
→ build prototype
→ evaluate output
→ improve and document
→ prepare repository, report and presentation
→ reflect on limitations and next steps
```

### 1. Explore possible topics

Start with broad interests. Which spatial patterns, changes, objects or relationships would you like to analyse? What kind of problem is meaningful for environmental monitoring, urban analysis, public administration or research?

At this stage, quantity matters. Collect several ideas before choosing one.

### 2. Formulate a spatial research question

Turn your topic into a question that can guide data, methods and evaluation. A good question is specific enough to implement, but open enough to require analysis.

For support, see [Research question](03_research-question.md).

### 3. Check data availability

A project idea becomes realistic only when suitable data exist and can be accessed in time. Check spatial coverage, time period, format, size, quality, licence and documentation.

Do this early. Data problems discovered late are difficult to fix.

### 4. Choose suitable methods

Choose methods because they fit the question and data, not only because they are technically interesting.

For example, {term}`Object Detection` fits questions about locating discrete objects, while {term}`Semantic Segmentation` fits questions about mapping classes for every pixel. Simpler baselines can also be useful, especially when they make the project more feasible.

### 5. Design the workflow

Plan the steps from raw data to final output. Include preprocessing, analysis, evaluation, visualisation and documentation.

For support, see [Workflow design](04_workflow-design.md).

### 6. Implement a first prototype

Start small. Test one file, one year, one tile, one class or one area before scaling up.

A prototype should answer: Can this workflow work in principle?

### 7. Evaluate outputs

Check whether your output is meaningful. Evaluation may include metrics, visual inspection, comparison with reference data, error analysis or a discussion of uncertainty.

The right evaluation depends on your project question and method.

### 8. Improve and document the workflow

Revise your project based on what you learn. Save important outputs, record decisions and keep your notebooks, scripts and README readable.

### 9. Prepare repository, report and Präsi

Your final submission includes a short report and a public GitHub or GitLab repository. You also present your project twice during the semester: first as a concept, later as an implemented workflow.

### 10. Reflect on limitations and next steps

A good project does not hide limitations. It explains what worked, what did not work, where uncertainty remains and what could be improved with more time.

---

## Iteration is expected

Project work is rarely linear. You may need to revise your question after checking data, simplify a method after testing code, reduce the study area because processing is too slow, or change the expected output after feedback.

This is normal. Iteration is not failure. It is how projects become feasible.

```{tip}
When something does not work, document what you tested and why you changed direction. Failed tests can be useful evidence for project decisions.
```

---

## What progress looks like

Progress can look like:

- a narrower research question,
- a confirmed data source,
- a smaller but realistic study area,
- a first data inventory,
- a workflow diagram,
- a prototype that works on one tile,
- an output that reveals a data problem,
- a figure that clarifies the project story,
- a README that explains how to run your code,
- a limitation that is clearly documented.

Do not wait for perfect results before documenting your work.

---

## Common pitfalls

Typical project lifecycle problems include:

- starting with a method instead of a question,
- choosing data too late,
- aiming for too much in one semester,
- postponing documentation,
- ignoring failed intermediate results,
- trying to make all code work only at the end,
- leaving repository cleanup until submission week,
- producing figures without a clear project argument.

---

## Mini task

Create a short project lifecycle sketch for your current idea.

Use this template:

```text
Topic:
Draft research question:
Likely data source:
Likely method:
First prototype:
Main output:
Evaluation idea:
Possible limitation:
Next decision point:
```

Then add one sentence:

```text
The first thing I need to test is ...
```

---

## Key takeaways

- A project lifecycle helps you manage decisions, not only tasks.
- Iteration is expected in spatial data science projects.
- Progress includes clearer decisions, better documentation and useful failed tests.
- Start small, test early and revise when needed.
- Your final project should show how your idea developed into an implemented workflow.
