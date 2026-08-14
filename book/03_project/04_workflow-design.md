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

---

## What is a spatial analytics workflow?

A spatial analytics workflow connects:

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

The workflow should show both technical steps and project logic. It should answer not only “What code do I run?” but also “Why does this step belong in the project?”

---

## Workflow diagram

A simple diagram can make your project easier to discuss during feedback sessions.

```{mermaid}
%%{init: {"flowchart": {"htmlLabels": true, "nodeSpacing": 60, "rankSpacing": 45}} }%%

flowchart TB
    A["<div style='width: 320px; text-align: center; line-height: 1.25;'><b>Research question</b><br/><em>What do I want to find out?</em></div>"]
    B["<div style='width: 320px; text-align: center; line-height: 1.25;'><b>Input data</b><br/><em>Which data can answer the question?</em></div>"]
    C["<div style='width: 320px; text-align: center; line-height: 1.25;'><b>Preprocessing</b><br/><em>How do I prepare the data?</em></div>"]
    D["<div style='width: 320px; text-align: center; line-height: 1.25;'><b>Analysis or model</b><br/><em>Which method fits the task?</em></div>"]
    E["<div style='width: 320px; text-align: center; line-height: 1.25;'><b>Output</b><br/><em>What does the workflow produce?</em></div>"]
    F["<div style='width: 320px; text-align: center; line-height: 1.25;'><b>Evaluation</b><br/><em>How do I check the result?</em></div>"]
    G["<div style='width: 320px; text-align: center; line-height: 1.25;'><b>Interpretation</b><br/><em>What does the result mean?</em></div>"]
    H["<div style='width: 320px; text-align: center; line-height: 1.25;'><b>Report and presentation</b><br/><em>How do I communicate it?</em></div>"]

    A --> B --> C --> D --> E --> F --> G --> H

    classDef workflowBox fill:#f8f9fa,stroke:#6c757d,stroke-width:1px,color:#111;
    class A,B,C,D,E,F,G,H workflowBox;
```

Adapt the diagram to your project. The goal is clarity, not decorative complexity.

---

## Design your workflow backwards

Start from the result you want to communicate.

Ask:

1. What is the main figure, map, metric or result?
2. What output is needed to create it?
3. Which method can produce that output?
4. Which input data are required?
5. Which preprocessing steps are needed?
6. Which checks are needed before I trust the output?

This prevents workflows that collect many data and methods without a clear final purpose.

```{tip}
Design a minimum viable workflow: the smallest version of your project that can produce a meaningful result.
```

---

## Minimum viable workflow

A minimum viable workflow might be:

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

## Decision points

| Decision | Example question |
| --- | --- |
| Data choice | Is the spatial or temporal resolution suitable? |
| Study area | Can the full area be processed, or should it be smaller? |
| Method | Is a complex model needed, or is a simpler baseline enough? |
| Evaluation | Do I have reference data or another way to assess output quality? |
| Output | Which figure or map best supports the research question? |

---

## Flags & checks

Use this table when your workflow becomes difficult to explain, rerun or connect to the research question.

| Red flag | First check |
| --- | --- |
| You cannot explain your project in 6–8 steps | Write the workflow as plain text before coding more. |
| Your method is clear but your question is not | Return to [Research question](03_research-question.md). |
| You have many datasets but no output plan | Design backwards from the final figure, map, metric or result. |
| Preprocessing is hidden or scattered | Make the sequence explicit and save important intermediate outputs. |
| Everything happens in one large notebook | Split exploration, preprocessing, analysis and results into clearer notebooks or scripts. |
| You have no evaluation step | Decide how you will judge whether the output is useful or trustworthy. |
| The workflow feels too complex to finish | Define a minimum viable workflow with one dataset, one method, one output and one check. |

---

## Mini task

Write your project workflow in 6–8 steps.

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
- Designing backwards keeps the project focused.
- A minimum viable workflow is better than an ambitious workflow that never runs.
- Workflow diagrams support feedback and reproducibility.
- Every project should include an evaluation or plausibility-check step.
