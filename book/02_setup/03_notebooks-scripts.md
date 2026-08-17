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

## 1. Why this distinction matters

In SDS320, your project code should do more than produce a result once. It should help you explore data, test ideas, document decisions, rerun important steps and explain your workflow to others.

You will probably use both {term}`Jupyter Notebooks <Jupyter Notebook>` and Python scripts. They serve different purposes:

- notebooks are useful for exploration, explanation, visual checks and project narration,
- scripts are useful for reusable logic, repeated processing and cleaner project organisation.

You do not need to choose one format for the whole project. A good project workflow often combines both.

---

## 2. The basic idea

A notebook is good for thinking with your code. A script is good for organising code that you want to reuse.

In early project work, you may start with notebooks because you are still exploring:

```text
What does the data look like?
Which columns or bands are available?
Does the map look reasonable?
Which preprocessing steps are needed?
Which method might work?
```

Later, repeated or stable parts of your workflow may move into scripts:

```text
clip raster
reproject vector data
create image tiles
calculate evaluation metrics
make a standard map
```

A useful SDS320 coding pathway often looks like this:

```text
explore in notebook
→ identify repeated code
→ turn repeated code into a function
→ move stable functions into a script
→ import the script into a notebook
→ use the notebook to explain and visualise the workflow
```

This pathway supports a clearer {term}`Data Pipeline` and improves {term}`Reproducibility`.

---

### A. What notebooks are good for

Use notebooks when you want to:

- explore a dataset,
- test an idea step by step,
- inspect an {term}`Intermediate Output`,
- create visual checks,
- combine code with written explanation,
- document decisions,
- prepare figures or maps,
- demonstrate parts of your workflow.

A notebook is especially useful when you are still learning what your data look like or when you want to explain your reasoning.

Good notebook names make the project sequence visible:

```text
notebooks/
  01_explore_data.ipynb
  02_preprocessing_tests.ipynb
  03_method_experiment.ipynb
  04_results_and_figures.ipynb
```

The numbers are helpful because they show the intended order. They also make it easier for someone else to understand where to start.

```{tip}
Use Markdown cells in notebooks to explain what a code block does, what you expected and what the output means. A notebook without explanation is often difficult to follow later.
```

---

### B. What scripts are good for

Use scripts when you want to:

- reuse a {term}`Function` across several notebooks,
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

Scripts are not only for large software projects. Even small SDS320 projects can benefit from one or two simple scripts.

A script can also become a Python {term}`Module` that you import into a notebook:

```python
from scripts.preprocessing import make_output_path
```

This example assumes that your project structure and Python import path support this import. If it does not work immediately, you can still copy the function into the notebook first and move it later.

<!-- TODO: Decide whether SDS320 should provide a standard way to import local scripts from notebooks. -->

---

### C. A practical SDS320 structure

Your exact project structure may differ, but a clear separation between notebooks, scripts, data and results is useful.

A simple SDS320 project could look like this:

```text
project/
├── README.md
├── environment.yml
├── data/
│   ├── README.md
│   ├── raw/
│   ├── processed/
│   └── training/
├── notebooks/
│   ├── 01_explore_data.ipynb
│   ├── 02_test_workflow.ipynb
│   └── 03_results_and_figures.ipynb
├── scripts/
│   ├── preprocessing.py
│   └── plotting.py
└── results/
    ├── figures/
    ├── maps/
    ├── predictions/
    └── evaluation/
```

This is a recommendation, not a strict rule. Adapt it to your project.

The important idea is that someone else should be able to answer these questions:

- Where do I start?
- Which notebook explains the workflow?
- Which scripts contain reusable code?
- Where are input data described?
- Where are results saved?
- Which environment should I use?

Your `README.md` should explain the intended order of notebooks and scripts. Your {term}`Environment File` should describe the software environment.

---

### D. Moving code from notebook to script

You do not need to move everything into scripts. Move code when it improves clarity.

Good candidates for scripts include code that:

- is used in several notebooks,
- processes many files,
- contains helper functions,
- is stable enough that you no longer need to inspect every line interactively,
- makes a notebook hard to read,
- should be rerun from start to finish.

#### Example: from notebook cell to helper function

A notebook cell might start like this:

```python
from pathlib import Path

input_path = Path("data/raw/raster/image_2026.tif")
output_path = Path("data/processed/image_2026_clipped.tif")

# many preprocessing steps here
```

If you repeat similar logic for several files, you can first turn part of it into a function:

```python
from pathlib import Path

def make_output_path(input_path, output_dir, suffix):
    """Create an output path based on an input file and a suffix."""
    input_path = Path(input_path)
    output_dir = Path(output_dir)
    output_dir.mkdir(parents=True, exist_ok=True)

    return output_dir / f"{input_path.stem}_{suffix}{input_path.suffix}"
```

Then your notebook can focus on the workflow:

```python
output_path = make_output_path(
    input_path="data/raw/raster/image_2026.tif",
    output_dir="data/processed",
    suffix="clipped",
)
```

If you use this function in several notebooks, move it into a script such as:

```text
scripts/
  paths.py
```

Then document in the notebook where the function comes from and why you use it.

```{tip}
A good rule of thumb: keep exploratory thinking in notebooks, but move repeated helper logic into scripts once it stabilises.
```

---

### E. Keeping notebooks readable

A notebook should not only run. It should also tell the reader what is happening.

Use a simple structure:

```text
1. Imports and project settings
2. Load data
3. Check data
4. Preprocess or analyse
5. Visualise outputs
6. Save results
7. Short interpretation
```

Useful notebook habits include:

- start with a short Markdown summary,
- keep imports near the top,
- define paths and parameters early,
- use section headings,
- explain important outputs,
- save figures and maps in predictable folders,
- end with a short summary of what the notebook produced.

Avoid mixing too many unrelated tasks in one notebook. If one notebook contains data search, preprocessing, model testing, final figures and report text, it will become difficult to rerun and review.

---

### F. Hidden notebook state

A common notebook problem is hidden state. This happens when a notebook only works because cells were executed in a special order that is not visible from top to bottom.

For example, a variable may exist because you created it earlier, deleted the cell and never restarted the kernel. The notebook still works for you, but it fails for someone else.

Before submitting your project, restart the kernel and run the notebook from the beginning.

In VS Code notebooks, use the {term}`restart-and-run-all <Restart and Run All>` command.

If the notebook fails, the issue is useful: it shows where your workflow is not yet reproducible.

```{warning}
A notebook that works only after manually running selected cells in a special order is difficult to reproduce.
```

---

### G. Scripts that are easy to reuse

A script should have a clear purpose. It does not need to be long or advanced.

A helpful script usually:

- has a descriptive file name,
- contains related functions,
- avoids project-specific absolute paths where possible,
- has short docstrings for important functions,
- avoids running large workflows automatically when imported,
- is mentioned in the `README.md` if it is part of the main workflow.

For example, `plotting.py` might contain reusable functions for map styling or figure export. `preprocessing.py` might contain functions for clipping, checking paths or preparing filenames.

```{warning}
Be careful with scripts that run a full analysis as soon as they are imported. This can make notebooks slow and confusing. Prefer scripts that define functions, then call those functions intentionally from a notebook or another script.
```

---

## 3. Flags & checks

Use this table when your notebooks or scripts become difficult to manage.

| Red flag | First check |
| --- | --- |
| One notebook has hundreds of cells | Split exploration, processing and results into separate notebooks. |
| You cannot remember which notebook to run first | Number notebooks and explain the order in the `README.md`. |
| The same code appears in several notebooks | Turn repeated logic into a function or script. |
| A notebook works only on your computer | Check for {term}`Absolute Path` values and replace them with {term}`Relative Path` values. |
| A notebook fails after restarting the kernel | Run cells from top to bottom and look for missing imports, paths or variables. |
| Outputs appear in unexpected folders | Check the {term}`Working Directory` and path definitions. |
| Scripts contain many unrelated tasks | Split them into smaller scripts with clearer names. |
| A script is required but not explained | Add it to the `README.md` and describe when to run or import it. |
| A figure appears without explanation | Add a Markdown cell or caption explaining what it shows and what it does not show. |
| Your repository is hard to understand | Add a workflow overview and link notebooks, scripts, data and results. |

For technical debugging, see [Troubleshooting](05_troubleshooting.md).

---

## 4. Mini task

Choose one notebook from your current or planned SDS320 project. If you do not yet have a project notebook, use your Python reactivation notebook.

Complete this checklist:

- [ ] Give the notebook a clear numbered file name.
- [ ] Add a short Markdown cell at the top explaining the notebook purpose.
- [ ] Check whether imports and project settings are near the top.
- [ ] Restart the kernel and run all cells from top to bottom.
- [ ] Identify one repeated or long code block.
- [ ] Decide whether that block should stay in the notebook, become a function or move into a script.
- [ ] Add one note to your `README.md` explaining where this notebook fits in the project workflow.

Write a short answer to this question:

```text
Which part of this notebook supports exploration, and which part should become more reusable?
```

---

## 5. Key takeaways

- Notebooks are useful for exploration, explanation and visual checks.
- Scripts are useful for reusable logic and repeated processing.
- Most SDS320 projects benefit from a combination of both.
- Restarting and rerunning notebooks helps reveal hidden-state problems.
- Repeated code is a signal that a function or script may be useful.
- Clear file names and a documented order make your repository easier to reproduce.
