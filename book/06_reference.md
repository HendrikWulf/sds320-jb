# Reference

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Quick lookup material for your project work
</div>
<!-- markdownlint-enable MD033 -->

---

## Why this section exists

The Reference section collects material that you may need repeatedly while developing your SDS320 project.

Unlike the lesson pages, these pages are not designed to be read from beginning to end. They are a place to return when you need to look up a term, reactivate a Python pattern, choose an evaluation approach, diagnose an error or find a useful data or documentation resource.

The aim is to help you solve routine problems quickly so that you can spend more time thinking about your research question, data and analytical decisions.

---

## How to use this section

Use the Reference section when you encounter a specific need.

For example:

- **What does this term mean?** → use the glossary.
- **How did that Python pattern work again?** → check the cheatsheets.
- **Which metric fits my model?** → use the model-evaluation guide.
- **Why is my workflow failing?** → start with the common-errors page.
- **Where could I find suitable spatial data?** → browse the datasets page.
- **Where can I learn more?** → use the curated further-reading resources.

The pages deliberately stay compact. When a topic needs more explanation, follow the links back to the relevant Setup, Project handbook or Lesson page.

---

## What you can find here

1. [Glossary](06_reference/01_glossary.md) — concise definitions of important terms used throughout SDS320.
2. [Cheatsheets](06_reference/02_cheatsheets.md) — quick reminders for Python, tabular data, vector data, rasters, project paths, figures and Git.
3. [Model evaluation](06_reference/03_model-evaluation.md) — practical guidance for choosing and interpreting evaluation approaches across different GeoAI tasks.
4. [Common errors](06_reference/04_common-errors.md) — frequent technical and conceptual problems, likely causes and useful first checks.
5. [Datasets](06_reference/05_datasets.md) — spatial data sources together with guidance for deciding whether a dataset actually fits your project.
6. [Further reading](06_reference/06_further-reading.md) — selected official documentation and stable resources for deeper learning.

---

## A note on using references

A reference page can remind you how a command works or what a metric measures. It cannot decide whether that command or metric is appropriate for your project.

For example, finding a dataset with the correct geographic coverage does not automatically mean that its {term}`spatial resolution <Spatial Resolution>` is suitable. Similarly, calculating several model metrics does not automatically create a meaningful evaluation.

Always connect the reference information back to your:

- research question,
- data,
- spatial and temporal scale,
- analytical method,
- expected output,
- interpretation.

Document important choices in your notebooks, report and repository rather than relying on the Reference section to explain them for you.

```{tip}
If you repeatedly need the same command, parameter or explanation while working on your project, add a short note to your own project documentation. Your repository should become a reference for your own workflow.
```

---

## Key message

Use these pages as a **project companion**: look things up when you need them, then return to your actual analysis. The goal is not to memorize every command or metric, but to understand your workflow well enough to choose, check and explain what you are doing.
