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

## 1. Why reproducibility matters

{term}`Reproducibility` means that someone else can understand and rerun the main steps of your project.

In SDS320, reproducibility matters because your final project includes a report and a public GitHub or GitLab repository. The repository should allow another student to understand your work on a different machine. This requires more than code that runs once on your laptop.

Reproducibility also helps you. A project that is documented and organised is easier to debug, revise, present and finish.

This page builds on the [SDS210 reproducibility material](https://hendrikwulf.github.io/sds210-jb/book/l12-reproducibility/) and adapts it to SDS320 project work.

---

## 2. What reproducibility means in this course

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

## 3. Reproducibility checklist

Use this checklist throughout the semester.

### Structure and paths

- [ ] The repository has a clear folder structure.
- [ ] Files and folders have meaningful names.
- [ ] Paths are relative where possible.
- [ ] Course material and project material are not mixed unnecessarily.

### Data and environment

- [ ] Data sources are documented in `data/README.md` or the main README.
- [ ] Original and processed data are separated.
- [ ] Manual download or preprocessing steps are described.
- [ ] Sensitive or restricted data are not shared publicly.
- [ ] The repository includes an environment file or setup instructions.

### Code and outputs

- [ ] Notebooks or scripts have a clear run order.
- [ ] Important notebooks can be restarted and run from top to bottom.
- [ ] Repeated code is moved into functions or scripts where useful.
- [ ] Important intermediate outputs are saved and named clearly.
- [ ] Figures, maps and evaluation outputs are saved in predictable folders.

### Interpretation and responsibility

- [ ] Results are connected to the research question.
- [ ] Evaluation steps are documented.
- [ ] Limitations and uncertainties are stated clearly.
- [ ] AI use is documented where applicable.

---

## 4. Reproducibility during the project

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

## 5. Flags & checks

| Red flag | First check |
| --- | --- |
| The project works only on your computer | Replace absolute paths and document setup steps. |
| You cannot remember how an output was produced | Link the output to the notebook or script that created it. |
| A notebook fails after restarting the kernel | Check imports, paths, variables and run order. |
| Data are missing from the repository | Document how to access or recreate them. |
| The README does not explain how to run the project | Add setup instructions and notebook/script order. |
| A result depends on manual editing | Describe the manual step or replace it with code. |
| AI helped debug important code | Add an honest AI-use note if relevant. |

---

## 6. Mini task

Identify one part of your current workflow that someone else could not yet reproduce.

Write:

```text
The least reproducible part of my project is ...
This is because ...
The next improvement is ...
```

Then make one small change: update a README, rename a file, add a path check, save an intermediate output or document a manual step.

---

## 7. Key takeaways

- Reproducibility is a practical project habit.
- Another student should be able to understand and rerun your main workflow.
- Relative paths, environment information, data documentation and run order matter.
- Build reproducibility gradually throughout the semester.
- Clear limitations are part of responsible project work.
