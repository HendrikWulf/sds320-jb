---
site:
  outline_maxdepth: 2
---

# Workflow design

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Planning the steps from raw data to interpretable results
</div>
<!-- markdownlint-enable MD033 -->

---

## Why workflow design matters

A workflow turns your research question into an implementable project. It shows how raw data become results, figures and interpretation.

Without a workflow, it is easy to jump between notebooks, tools and ideas without knowing what depends on what. With a workflow, you can implement the project step by step, debug problems more easily, explain your decisions and improve {term}`Reproducibility`.

A good workflow also helps you identify what can be simplified when time, data or computing power become limiting.

---

## What is a spatial analytics workflow?

A spatial analytics workflow connects:

- input data,
- preprocessing,
- analysis or model,
- evaluation,
- visualisation,
- interpretation,
- documentation.

In SDS320, the workflow should show both technical steps and project logic. It should answer not only “What code do I run?” but also “Why does this step belong in the project?”

---

## A simple workflow template

A general project workflow can be written as:

```text
Question
→ Data
→ Preprocessing
→ Method
→ Output
→ Evaluation
→ Interpretation
→ Communication
```

In practice, this might become:

```text
Define study area
→ download imagery and vector context data
→ clip and reproject data
→ create tiles or features
→ run model or analysis
→ compare output with reference data
→ create map and figure
→ interpret pattern and limitations
```

---

## Workflow diagram

A simple diagram can make your project easier to discuss during feedback sessions.

```{mermaid}
flowchart LR
    A[Research question] --> B[Input data]
    B --> C[Preprocessing]
    C --> D[Analysis or model]
    D --> E[Output]
    E --> F[Evaluation]
    F --> G[Interpretation]
    G --> H[Report and presentation]
```

You can adapt this diagram to your project. The goal is clarity, not decorative complexity.

---

## Designing your workflow backwards

One useful strategy is to start from the end.

Ask:

1. What is the main figure, map, metric or result I want to show?
2. What output do I need to create that figure or metric?
3. Which method can produce that output?
4. Which input data are required?
5. Which preprocessing steps are needed before the method works?
6. Which checks are needed to trust the output?

This prevents workflows that collect many data and methods without a clear final purpose.

```{tip}
Design at least one “minimum viable workflow”: the smallest version of your project that can produce a meaningful result.
```

---

## Example: minimum viable workflow

A minimum viable workflow is a reduced version of your project that works on a small example.

```text
One study area
→ one input dataset
→ one preprocessing step
→ one method
→ one output map
→ one evaluation or plausibility check
→ one caption explaining the result
```

Once this works, you can decide whether to expand.

---

## What to include in a workflow diagram

A useful workflow diagram usually includes:

- input datasets,
- main preprocessing steps,
- analysis or model step,
- important intermediate outputs,
- evaluation step,
- final outputs,
- software or notebooks where helpful,
- decision points or possible fallback options.

Avoid making the diagram too detailed. If every minor operation appears, the main logic becomes hard to see.

---

## Intermediate outputs

Save important {term}`Intermediate Outputs <Intermediate Output>` when they help you debug or rerun the project.

Examples include:

- clipped study-area data,
- reprojected layers,
- training tiles,
- cleaned attribute tables,
- first model predictions,
- evaluation tables,
- draft figures.

Do not save every temporary file forever. Keep outputs that support reproducibility, evaluation or communication.

---

## Decision points

Your workflow should show where decisions may be needed:

| Decision | Example question |
| --- | --- |
| Data choice | Is the spatial or temporal resolution suitable? |
| Study area | Can the project be completed for the full area or should it be smaller? |
| Method | Is a complex model needed, or is a simpler baseline enough? |
| Evaluation | Do I have reference data or another way to assess output quality? |
| Output | Which figure or map best supports the research question? |

---

## Common pitfalls

Typical workflow problems include:

- hidden preprocessing steps,
- unclear intermediate outputs,
- trying to do everything in one notebook,
- not saving important intermediate results,
- no evaluation step,
- no link between method and question,
- workflows that depend on manual clicks but are not documented,
- final figures that are not connected to the research question.

---

## Flags & checks

| Red flag | First check |
| --- | --- |
| You cannot explain your project in 6–8 steps | Write the workflow as plain text before coding more. |
| Your method is clear but your question is not | Return to [Research question](03_research-question.md). |
| You have many datasets but no output plan | Design backwards from the final figure or map. |
| Your workflow only works after manual steps | Document those steps or replace them with code where possible. |
| You have no evaluation step | Decide how you will judge whether the output is useful. |
| Your notebook order is unclear | Use numbered notebooks and document the run order in the README. |

---

## Mini task

Write your project workflow in 6–8 steps.

Use this template:

```text
1. ...
2. ...
3. ...
4. ...
5. ...
6. ...
```

Then add:

```text
Main output:
Evaluation check:
Most fragile step:
Fallback if this step fails:
```

---

## Key takeaways

- A workflow connects your question, data, method, output and interpretation.
- Designing backwards helps keep the project focused.
- A minimum viable workflow is better than an ambitious workflow that never runs.
- Workflow diagrams support feedback and reproducibility.
- Every project should include an evaluation or plausibility-check step.
