---
site:
  outline_maxdepth: 2
---

# Reproducibility

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Making your project understandable and rerunnable
</div>
<!-- markdownlint-enable MD033 -->

---

## Why reproducibility matters

{term}`Reproducibility` means that someone else can understand and rerun the main steps of your project.

In SDS320, reproducibility matters because your final project includes a report and a public GitHub or GitLab repository. The repository should allow another student to understand your work on a different machine. This requires more than code that runs once on your laptop.

Reproducibility also helps you. A project that is documented and organised is easier to debug, revise, present and finish.

This page builds on the SDS210 reproducibility material and adapts it to SDS320 project work.

<!-- TODO: link directly to the relevant SDS210 reproducibility pages once the final cross-course link structure is confirmed. -->

---

## What reproducibility means in this course

In SDS320, reproducibility does not mean that every large dataset must be stored in your repository. It means that the main workflow is understandable and rerunnable from documented materials.

Another student should be able to answer:

- What is the project about?
- Which data were used?
- Where do the data come from?
- Which environment or packages are needed?
- Which notebooks or scripts should be run?
- In what order should they be run?
- Which outputs are produced?
- Which steps are manual or uncertain?
- What are the main limitations?

---

## Reproducibility checklist

Use this checklist throughout the semester.

### Project structure

- [ ] The repository has a clear folder structure.
- [ ] Files and folders have meaningful names.
- [ ] Course material and project material are not mixed unnecessarily.
- [ ] Large raw data are not committed unless this is allowed and practical.

### Paths and data

- [ ] Paths are relative where possible.
- [ ] Data sources are documented in `data/README.md` or the main README.
- [ ] Original data and processed data are separated.
- [ ] Manual download or preprocessing steps are described.
- [ ] Sensitive or restricted data are not shared publicly.

### Environment

- [ ] The repository includes an environment file or setup instructions.
- [ ] Package versions are documented where relevant.
- [ ] The active environment is named clearly.
- [ ] Unusual installation steps are described.

### Code workflow

- [ ] Notebooks or scripts have a clear run order.
- [ ] Important notebooks can be restarted and run from top to bottom.
- [ ] Repeated code is moved into functions or scripts where useful.
- [ ] Intermediate outputs are saved when they help rerun the workflow.
- [ ] Code comments explain non-obvious decisions.

### Outputs and interpretation

- [ ] Figures and maps are saved in predictable folders.
- [ ] Results are connected to the research question.
- [ ] Evaluation steps are documented.
- [ ] Limitations and uncertainties are stated clearly.
- [ ] AI use is documented where applicable.

---

## Reproducibility during the project

Do not wait until the final week to make your project reproducible. Build reproducibility gradually.

A useful rhythm is:

```text
after each meaningful project step
→ commit changes
→ update README or notes
→ check paths
→ save important outputs
→ write down decisions
```

For example:

- after data acquisition, document source, date, licence and access route,
- after preprocessing, explain what changed and why,
- after first modelling, save model settings or parameters,
- after evaluation, save metrics and figures,
- after feedback, note what you changed.

```{tip}
Your future self is also a user of your project. Write instructions so that you can understand them again two weeks later.
```

---

## Common pitfalls

Typical reproducibility problems include:

- local absolute paths,
- undocumented manual steps,
- hidden notebook state,
- missing data instructions,
- unclear package versions,
- notebooks that only run when cells are executed in a special order,
- outputs that were created manually but not explained,
- figures without information about input data or processing steps,
- AI-generated code that was not checked or documented.

---

## Red flags and first checks

| Red flag | First check |
| --- | --- |
| The project works only on your computer | Replace absolute paths and document setup steps. |
| You cannot remember how an output was produced | Add workflow notes and link output to notebook/script. |
| A notebook fails after restarting the kernel | Check imports, paths, variables and run order. |
| Data are missing from the repository | Document how to access or recreate them. |
| A package import works on one machine but not another | Check the environment file and selected kernel. |
| The README does not explain how to run the project | Add a short run order and setup section. |
| A result depends on manual editing | Describe the manual step or replace it with code. |
| AI helped debug important code | Add an honest AI-use note if relevant. |

---

## Decision points

Decide early:

- Which data can be shared publicly?
- Which data must be downloaded separately?
- Which outputs should be saved?
- Which notebooks are exploratory and which are part of the main workflow?
- Which scripts are essential?
- What does another student need to know before running the project?

---

## Mini task: reproducibility gap

Identify one part of your current workflow that someone else could not yet reproduce.

Write:

```text
The least reproducible part of my project is ...
This is because ...
The next improvement is ...
```

Then make one small change: update a README, rename a file, add a path check, save an intermediate output or document a manual step.

---

## Key takeaways

- Reproducibility is a practical project habit, not a final decoration.
- Another student should be able to understand and rerun your main workflow.
- Relative paths, environment information, data documentation and run order matter.
- Build reproducibility gradually throughout the semester.
- Clear limitations are part of reproducible and responsible project work.
