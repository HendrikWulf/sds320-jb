---
site:
 outline_maxdepth: 2
---

# Notebooks and scripts

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Choosing the right format for exploration, analysis and reproducibility
</div>
<!-- markdownlint-enable MD033 -->

---

## Why this distinction matters

In SDS320, you will likely use both notebooks and scripts.

Notebooks are useful for exploration, explanation and visual checks. Scripts are useful for reusable functions, repeated processing and cleaner project organisation.

You do not need to choose only one format. A good project often combines both.

---

## Notebooks are good for

Use notebooks when you want to:

- explore a dataset,
- test an idea step by step,
- inspect intermediate outputs,
- create visual checks,
- combine code with explanation,
- document decisions,
- prepare figures or maps,
- demonstrate parts of your workflow.

A notebook is especially useful when you are still learning what your data look like or when you want to explain your reasoning.

Example notebook names:

```text
notebooks/
  01_explore_data.ipynb
  02_preprocessing_tests.ipynb
  03_method_experiment.ipynb
  04_results_and_figures.ipynb
```

---

## Scripts are good for

Use scripts when you want to:

- reuse functions across notebooks,
- process many files,
- run the same workflow repeatedly,
- keep long code blocks out of notebooks,
- separate helper functions from analysis narrative,
- make your repository easier to understand.

Example script names:

```text
scripts/
  preprocessing.py
  features.py
  evaluation.py
  plotting.py
```

Scripts are not only for professional software projects. Even small SDS320 projects can benefit from one or two simple scripts.

---

## A practical SDS320 workflow

A simple project structure could look like this:

```text
my-sds320-project/
├── README.md
├── environment.yml
├── notebooks/
│   ├── 01_explore_data.ipynb
│   ├── 02_test_workflow.ipynb
│   └── 03_results_and_figures.ipynb
├── scripts/
│   ├── preprocessing.py
│   └── plotting.py
├── data/
│   └── README.md
├── outputs/
└── figures/
```

This is a recommendation, not a strict rule. Adapt it to your project.

The important idea is that someone else should be able to understand where to start, where the main workflow lives and where outputs are saved.

---

## Moving code from notebook to script

You do not need to move everything into scripts. Move code when it improves clarity.

Good candidates for scripts include code that:

- is used in several notebooks,
- processes many files,
- contains helper functions,
- is stable enough that you no longer need to inspect every line interactively,
- makes a notebook hard to read,
- should be rerun from start to finish.

### Example: from notebook cell to helper function

A notebook cell might start like this:

```python
from pathlib import Path

input_path = Path("data/image_2026.tif")
output_path = Path("outputs/image_2026_clipped.tif")

# many preprocessing steps here
```

If you repeat similar logic for several files, you can move part of it into a function:

```python
from pathlib import Path

def make_output_path(input_path, output_dir, suffix):
    input_path = Path(input_path)
    output_dir = Path(output_dir)
    output_dir.mkdir(exist_ok=True)

    return output_dir / f"{input_path.stem}_{suffix}{input_path.suffix}"
```

Then your notebook can focus on the workflow:

```python
output_path = make_output_path(
    input_path="data/image_2026.tif",
    output_dir="outputs",
    suffix="clipped",
)
```

---

## Hidden notebook state

A common notebook problem is hidden state. This happens when a notebook only works because cells were executed in a particular order that is not visible from top to bottom.

Before submitting your project, restart the kernel and run the notebook from the beginning.

In JupyterLab, use:

```text
Kernel → Restart Kernel and Run All Cells
```

If the notebook fails, the issue is useful: it shows where your workflow is not yet reproducible.

```{warning}
A notebook that works only after manually running selected cells in a special order is difficult to reproduce.
```

---

## Common pitfalls

### One huge notebook

A single long notebook can become hard to debug. Consider splitting exploration, preprocessing, method testing and final figures into separate notebooks.

### Too many disconnected notebooks

The opposite problem is also possible. Many notebooks with unclear names can make the workflow hard to follow. Use numbered names and explain the order in your README.

### Absolute paths

Absolute paths make notebooks difficult to run on another computer. Prefer project-relative paths.

### Outputs without explanation

A map or figure is not enough on its own. Add Markdown cells explaining what the output shows and why it matters for your project.

### Scripts that nobody can run

Scripts should have clear names and simple responsibilities. If a script requires a specific command or input file, document it in the README.

---

## Mini task

Open one notebook from your current or planned SDS320 project.

Identify one part that could become:

- a reusable function,
- a separate script,
- a clearer notebook section,
- or a Markdown explanation.

Write a short note explaining why this change would improve your project.

---

## Key takeaways

- Notebooks are useful for exploration, explanation and visual checks.
- Scripts are useful for reusable logic and repeated processing.
- You can combine both in one project.
- Restarting and rerunning notebooks helps reveal hidden-state problems.
- Clear file names and a documented order make your repository easier to reproduce.
