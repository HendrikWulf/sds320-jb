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

Spatial data science projects become easier to manage when you treat them as a sequence of decisions, tests and revisions.

At the start, your project may feel like a broad idea: urban heat, land-cover change, flood risk, vegetation monitoring, object detection or satellite embeddings. The lifecycle helps you turn that idea into a feasible workflow with a question, data, method, output and interpretation.

Progress is not only “working code”. Progress can also mean a clearer question, a rejected dataset, a smaller study area, a documented limitation or a figure that reveals a problem.

---

## The SDS320 project lifecycle

A useful SDS320 lifecycle looks like this:

```text
→ Explore topic
→ formulate question
→ check data
→ choose method
→ design workflow
→ build prototype
→ evaluate output
→ document and improve
→ prepare repository, report and presentation
→ reflect on limitations
```

### 1. Explore possible topics

Start with several broad interests. Which spatial patterns, changes, objects or relationships would you like to analyse? Which applied problem could your project help explain?

Collect options before choosing one. Early variety makes it easier to find a feasible project.

### 2. Formulate a spatial research question

Turn the topic into a question that guides data, methods and evaluation. A good question is specific enough to implement, but open enough to require analysis.

See [Research question](03_research-question.md).

### 3. Check data availability

A project becomes realistic only when suitable data exist and can be accessed in time. Check spatial coverage, time period, format, size, quality, licence and documentation early.

### 4. Choose suitable methods

Choose methods because they fit your question and data. Complex methods are not automatically better. A simpler baseline can make your project more robust and easier to interpret.

See [Data, methods and scope](05_data-methods-scope.md).

### 5. Design the workflow

Plan how raw data become results. Include preprocessing, analysis, evaluation, visualisation and documentation.

See [Workflow design](04_workflow-design.md).

### 6. Build a first prototype

Start small. Test one file, one year, one tile, one class or one area before scaling up.

A prototype answers one question: can this workflow work in principle?

### 7. Evaluate the output

Check whether the output is useful and trustworthy. Depending on your project, this may involve metrics, visual inspection, comparison with reference data, error analysis or uncertainty discussion.

### 8. Document and improve

Revise the project based on what you learn. Save important outputs, record decisions and keep notebooks, scripts and README files readable.

### 9. Prepare repository, report and Präsi

Your final project includes a short report and a public GitHub or GitLab {term}`Repository`. You also present your project twice: first as a concept, later as an implemented workflow.

### 10. Reflect on limitations

A good project does not hide limitations. It explains what worked, what did not work, where uncertainty remains and what could be improved with more time.

---

## Iteration is expected

Project work is rarely linear. You may revise your question after checking data, simplify a method after testing code, reduce the study area because processing is too slow, or change the expected output after feedback.

```{tip}
When something does not work, document what you tested and why you changed direction. A well-explained failed test can support a good project decision.
```

---

## What progress looks like

Progress can mean:

- a narrower research question,
- a confirmed data source,
- a first data inventory,
- a working prototype on a small example,
- a workflow diagram,
- a draft figure,
- a documented limitation,
- a README that explains the current run order,
- a clear next decision.

Do not wait for perfect results before documenting your work.

---

## Flags & checks

| Red flag | First check |
| --- | --- |
| You started with a method but no question | Return to [Research question](03_research-question.md). |
| You have not checked data availability | Find one candidate dataset and test whether you can open it. |
| The project has too many goals | Define the smallest version that could still produce a meaningful result. |
| Documentation is postponed | Add one short note after each important decision or test. |
| You ignore failed intermediate results | Record what failed and what this means for your next step. |
| Repository cleanup is left until the end | Start the README and folder structure now. |

---

## Mini task

Create a short lifecycle sketch for your current idea.

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

Then add:

```text
The first thing I need to test is ...
```

---

## Key takeaways

- A project lifecycle helps you manage decisions, not only tasks.
- Iteration is expected.
- Start small, test early and revise when needed.
- Document decisions while they happen.
- Your final project should show how your idea developed into an implemented workflow.
