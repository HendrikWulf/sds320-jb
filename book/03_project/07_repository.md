---
site:
  outline_maxdepth: 2
---

# Repository

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Organising your SDS320 project so others can understand and rerun it
</div>
<!-- markdownlint-enable MD033 -->

---

## Why the repository matters

Your SDS320 project must include a public GitHub or GitLab {term}`Repository`. The report must include a link to this repository.

The repository is part of the assessed project submission. It should allow another student to understand your project and reproduce the main workflow on a different machine.

This page explains what the repository should contain. It does not replace [Git basics](../02_setup/04_git-basics.md), which explains the basic Git workflow.

---

## What your repository should contain

Based on the SDS320 syllabus, your repository should include:

- a clear `README.md`,
- setup instructions,
- information about the required Python environment,
- notebooks or scripts that can be executed from start to finish,
- relative paths where possible,
- readable and well-structured code,
- helpful comments where needed,
- functions, methods or classes where they improve the workflow,
- data instructions,
- figures or outputs where useful.

Your repository should not be a random file dump. It should tell the reader what matters and how the pieces fit together.

---

## Suggested repository structure

The structure below is a recommendation, not a strict rule. Adapt it to your project.

```text
my-sds320-project/
├── README.md
├── environment.yml
├── .gitignore
├── data/
│   ├── README.md
│   ├── raw/
│   ├── processed/
│   └── training/
├── notebooks/
│   ├── 01_explore_data.ipynb
│   ├── 02_preprocess_data.ipynb
│   └── 03_results_and_figures.ipynb
├── scripts/
│   ├── preprocessing.py
│   └── plotting.py
├── results/
│   ├── figures/
│   ├── maps/
│   ├── predictions/
│   └── evaluation/
└── report/
```

You may use a different structure if it is clearer for your project. The important part is that another person can understand where to start, which files are essential and which outputs belong to the final project.

---

## README structure

Your `README.md` is the entry point to your project. Start it early and improve it throughout the semester.

A useful README outline is:

```markdown
# Project title

---
## Short summary

One short paragraph explaining the project.

---
## Research question

State the final research question.

---
## Data sources

List the datasets, providers, access routes, dates, formats and licences where relevant.

---
## Methods

Briefly explain the main workflow and method choices.

---
## Repository structure

Explain the main folders and important files.

---
## How to run

Explain the environment setup and the order of notebooks or scripts.

---
## Results

Summarise the key result and link to important figures or outputs.

---
## Limitations

State important limitations, uncertainties or incomplete parts.

---
## AI use

State whether and how AI tools were used, if applicable.

---
## Licence and citation

Explain how the project code, figures or data may be reused, if applicable.
```

Do not wait until the end to write the README. A rough README is useful already during project development.

---

## Data instructions

Large raw data files should usually not be committed. Instead, document how to get them.

Your `data/README.md` can include:

```text
Dataset name:
Provider:
URL or access route:
Download date:
Spatial coverage:
Temporal coverage:
Format:
Coordinate reference system:
Licence:
Processing notes:
Can the data be shared publicly?
```

```{warning}
Do not commit sensitive data, private data, passwords, access tokens or API keys. If this happens, deleting the file later may not remove it from the Git history.
```

---

## What not to upload

Avoid uploading:

- very large raw data files,
- private or sensitive data,
- passwords,
- access tokens,
- API keys,
- local cache folders,
- temporary files,
- system files such as `.DS_Store`,
- outputs that are huge and easy to recreate,
- files that you do not understand.

Use a {term}`Gitignore File` to exclude files and folders that should not be tracked.

---

## Code quality in the repository

Your code should be readable and sufficiently documented.

This usually means:

- meaningful file names,
- meaningful variable names,
- imports near the top,
- project settings visible,
- paths that are relative where possible,
- notebooks that can run from top to bottom,
- scripts with focused functions,
- comments for non-obvious decisions,
- no large blocks of unused code,
- no hidden manual steps without explanation.

For code organisation, see [Notebooks and scripts](../02_setup/03_notebooks-scripts.md).

---

## Flags & checks

| Red flag | First check |
| --- | --- |
| The README is empty | Add title, summary, question, data, method and run order. |
| The repository has many files but no structure | Group files into notebooks, scripts, data, results and report folders. |
| The project only runs on your computer | Check for absolute paths and missing environment information. |
| Data are uploaded but licence is unclear | Remove or restrict data and document access instead. |
| The main workflow is hidden in several notebooks | Add a run order to the README. |
| Notebooks fail after restarting the kernel | Fix hidden-state problems before final submission. |
| The repository contains passwords or tokens | Stop and ask for help before continuing. |
| Code is copied from AI or websites without explanation | Review, adapt, cite or disclose according to the course AI and integrity guidance. |

---

## Mini task

Open your project repository and improve the README.

Complete at least these sections:

```text
Project title:
Research question:
Data sources:
Main method:
How to run:
Current limitations:
```

Then make one Git commit with a clear message, for example:

```text
Update README with project question and data sources
```

---

## Key takeaways

- The repository is part of the assessed SDS320 project.
- It should support understanding and reproducibility, not only file storage.
- A clear README is essential.
- Data sources and setup steps need documentation.
- Avoid committing large data, sensitive data or credentials.
- Repository cleanup is much easier when it starts early.
