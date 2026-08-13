---
site:
  outline_maxdepth: 1
---

# Troubleshooting

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A practical guide for when your code, environment or data workflow breaks
</div>
<!-- markdownlint-enable MD033 -->

---

## Why errors matter

Errors are normal in spatial data science. They are not a sign that you are bad at programming.

SDS320 projects combine Python, packages, notebooks, spatial data, file paths, repositories and sometimes machine learning workflows. Many things can break. Good troubleshooting helps you find the likely cause step by step instead of changing many things at once.

Troubleshooting is also part of {term}`Reproducibility`. If you understand why something failed, you can document the fix and avoid the same problem later.

This page helps you:

1. slow down when an error appears,
2. identify the likely source of the problem,
3. test one possible cause at a time,
4. ask useful questions,
5. use AI tools responsibly when debugging.

---

## A troubleshooting pathway

When something breaks, use this routine.

```text
read the error
→ find the failing line
→ check the environment
→ check paths and files
→ test a smaller example
→ inspect spatial assumptions
→ search course material or documentation
→ ask a clear question
→ document the fix
```

---

### 1. Read the error message

Start at the bottom of the error message. Python usually shows the most direct error there.

Look for:

- the error type,
- the file or notebook cell,
- the line that failed,
- the object, package, file or variable mentioned.

Common error types include:

```text
ModuleNotFoundError
FileNotFoundError
NameError
ValueError
TypeError
ImportError
```

The error message is not just noise. It is usually the best starting point.

---

### 2. Identify the line that failed

Do not debug the whole notebook at once. Find the specific line that caused the error.

Ask yourself:

- Which line failed?
- Which object was used on that line?
- Was the object created earlier?
- Does the object have the expected type, shape, CRS or path?
- Did the previous cell actually run?

For example, if this line fails:

```python
gdf = gpd.read_file(input_path)
```

the problem may be:

- `gpd` was not imported,
- `input_path` is wrong,
- the file does not exist,
- the file format is unsupported,
- the notebook uses the wrong environment,
- the file is corrupted or incomplete.

---

### 3. Check the active environment

Many package errors come from using the wrong Python environment.

In the terminal, run:

```bash
conda info --envs
```

The active environment should be marked with an asterisk. For SDS320, you usually want `sds320`.

In Python, run:

```{code-cell} python
import sys

print(sys.executable)
```

Check whether the path points to your `sds320` {term}`Conda Environment`.

If imports fail in the notebook but work in the terminal, your notebook may use the wrong {term}`Jupyter Kernel`.

---

### 4. Check paths and files

Many spatial data workflows fail because a file path is wrong.

Run:

```{code-cell} python
from pathlib import Path

path = Path("data/example.tif")

print("Working directory:", Path.cwd())
print("Path to check:", path)
print("Exists:", path.exists())
```

If `path.exists()` returns `False`, Python cannot find the file from the current {term}`Working Directory`.

```{warning}
A file path that works on your computer may fail on another computer if it is an {term}`Absolute Path`. Prefer project-relative paths where possible.
```

For more on paths, see [Python reactivation](02_python-reactivation.md).

---

### 5. Run a smaller example

If a workflow fails on a large dataset, test the same logic on something smaller.

Instead of processing:

```text
all scenes
all bands
all tiles
all polygons
the full study area
```

try:

```text
one scene
one band
one tile
five polygons
a small test area
```

Small examples help you separate coding problems from data-size problems.

```{tip}
A smaller example is not wasted time. It is often the fastest way to find the actual problem.
```

---

### 6. Inspect spatial assumptions

Spatial workflows can produce wrong results even when Python does not raise an error.

Before trusting the output, check:

- {term}`Coordinate Reference System (CRS)`,
- geometry validity,
- raster shape,
- raster bounds,
- raster {term}`NoData Value`,
- band order,
- value ranges,
- spatial overlap,
- expected units.
- visual plausibility.

A map that looks plausible is not proof that the analysis is correct, but a map that looks wrong is a useful warning.

---

## Common error categories

### A. Environment and package errors

Common symptoms:

```text
ModuleNotFoundError: No module named 'geopandas'
ImportError: ...
```

Likely causes:

- wrong notebook kernel,
- environment not activated,
- package not installed,
- package installed in a different environment.

First checks:

```bash
conda activate sds320
python -c "import geopandas; print(geopandas.__version__)"
```

If this works in the terminal but not in your notebook, the notebook likely uses a different kernel.

Red flags:

- package imports work in one place but not another,
- `sys.executable` does not contain `sds320`,
- VS Code shows a different Python interpreter,
- the environment was updated but the notebook was not restarted.

---

### B. Jupyter kernel errors

Common symptoms:

- the kernel does not start,
- imports fail only in notebooks,
- the environment is missing from the kernel list,
- outputs depend on cells executed earlier.

First checks:

```bash
conda activate sds320
python -m ipykernel install --user --name sds320 --display-name "Python (sds320)"
```

Then restart JupyterLab or VS Code.

Also try:

```text
Kernel → Restart Kernel and Run All Cells
```

This reveals {term}`Hidden Notebook State` problems.

For more on this, see [Notebooks and scripts](03_notebooks-scripts.md).

---

### C. Path and file errors

Common symptoms:

```text
FileNotFoundError
No such file or directory
```

First checks:

```{code-cell} python
from pathlib import Path

print(Path.cwd())
print(list(Path(".").iterdir())[:10])
```

Then check whether the file path is correct relative to the current working directory.

Useful checks:

```{code-cell} python
from pathlib import Path

input_path = Path("data/raw/example.tif")

print(input_path)
print(input_path.exists())
print(input_path.resolve())
```

Red flags:

- the path contains your username,
- the path starts from Desktop or Downloads,
- the file exists in Finder or Explorer but Python cannot see it,
- code works only when the notebook is opened from one specific folder.

---

### D. CRS and spatial data errors

Common symptoms:

- layers do not overlap on a map,
- spatial joins return no matches,
- distances or areas look wrong,
- maps appear in unexpected locations.

Likely causes:

- missing CRS,
- different CRS between layers,
- incorrect CRS assigned instead of reprojected,
- invalid geometries.

First checks:

```python
print(gdf.crs)
print(other_gdf.crs)
```

If both layers have different CRS, reproject one layer to match the other before spatial operations.

```python
other_gdf = other_gdf.to_crs(gdf.crs)
```

This example is illustrative and requires existing GeoDataFrames.

```{warning}
Do not simply assign a CRS to “make the error disappear”. Assigning a CRS and reprojecting a dataset are different operations.
```

---

### E. Raster size or memory errors

Common symptoms:

- the kernel crashes,
- processing is very slow,
- memory errors appear,
- large arrays cannot be loaded.

Likely causes:

- reading a whole raster into memory,
- working with many large files at once,
- using very high-resolution data for a broad study area,
- not clipping or tiling data before analysis.

First checks:

- inspect raster dimensions before reading all data,
- test on a smaller area,
- process one file at a time,
- save {term}`Intermediate Output`,
- reduce spatial extent,
- reduce number of bands,
- check whether tiling is needed.

Useful questions:

- Do I need the full raster for this step?
- Can I clip to the area of interest first?
- Can I test on one tile?
- Can I save a processed file and continue from there?

---

### F. Machine learning workflow errors

Common symptoms:

- shape mismatch errors,
- labels and images do not align,
- training runs but results are meaningless,
- validation performance is unrealistically high.

Likely causes:

- input arrays have unexpected dimensions,
- labels use a different resolution or extent,
- training and test areas are spatially too similar,
- classes are strongly imbalanced,
- preprocessing differs between training and prediction,
- labels or masks are misaligned with the imagery.

First checks:

- print array shapes,
- plot image and label together,
- inspect class counts,
- check train/validation/test split logic,
- test the workflow on a few samples first,
- check for {term}`Data Leakage`.

```{warning}
A model that gives very high validation scores can still be wrong if the validation data are too similar to the training data or if labels leaked into the workflow.
```

---

### G. Git errors

Common symptoms:

```text
fatal: not a git repository
nothing to commit
rejected
merge conflict
```

First checks:

```bash
pwd
git status
git remote -v
```

Make sure you are inside the project folder and that the remote repository is configured.

For more help, see [Git basics](04_git-basics.md).

---

## Troubleshooting with an AI tool

AI tools can be useful for debugging, but they are not a substitute for understanding your own code. Use them as a structured helper, not as an automatic fix button.

There are two common pathways:

1. ask an external large language model in a browser,
2. use an AI coding assistant inside VS Code,

```{warning}
Do not paste private data, passwords, API keys, access tokens, unpublished student data or sensitive project material into external AI tools. When in doubt, remove file paths, credentials and data samples before asking.
```

### What AI tools are good at

AI tools can help you:

- explain an error message,
- identify likely causes,
- suggest first checks,
- simplify a confusing function,
- compare two versions of code,
- propose a smaller test case,
- draft a better help request,
- explain a warning in plain language.

They are less reliable for:

- knowing your exact file structure,
- understanding unpublished course instructions,
- checking whether your data are scientifically appropriate,
- judging whether your spatial result is meaningful,
- detecting all privacy or licensing problems,
- guaranteeing that generated code is correct.

### Before asking an AI tool

Collect the context first:

```text
What I am trying to do:
The code that failed:
The full error message:
What I expected:
What happened instead:
My operating system:
My active conda environment:
What I already checked:
```

Remove or anonymise:

- personal file paths,
- tokens and passwords,
- private repository URLs,
- sensitive data,
- large raw data samples,
- information that should not leave the university environment.

### Pathway A: external LLM in a browser

Use this when you want a plain-language explanation or a debugging plan.

Example prompt:

```text
I am working on a spatial data science project in Python. I am trying to read a vector file with GeoPandas.

Goal:
I want to load a GeoPackage and inspect its CRS.

Code:
[copy only the relevant few lines]

Error message:
[paste the full error message]

What I already checked:
- The file exists according to pathlib.
- My notebook uses the sds320 kernel.
- GeoPandas imports successfully.

Please explain the most likely causes and suggest three checks before changing the code.
```

A good AI answer should help you decide what to test next. Do not copy a long block of generated code into your project without understanding it.

### Pathway B: internal AI assistant

VS Code can integrate AI coding assistants. The most common option is GitHub Copilot. Depending on your installation and account, Copilot Chat may support project questions, file references, terminal questions, `/fix`, and other code-improvement commands.

Useful VS Code extensions or features include:

- **GitHub Copilot Chat** or the built-in Copilot Chat experience, depending on your VS Code setup,
- **Continue**, an open-source AI code assistant extension for VS Code,
- **Blackbox Agent**, capable of creating/editing files, running commands and using the browser.

Use a VS Code assistant when the relevant files are already open and you want the tool to inspect selected code or the current workspace.

Good use cases:

- explain the selected code,
- explain the last terminal error,
- suggest why a notebook cell fails,
- refactor a repeated code block,
- add comments to a helper function,
- propose a small test,
- compare two related scripts.

Good prompts:

```text
Explain this error and suggest what I should check first. Do not rewrite the whole workflow.
```

```text
I selected a function from my preprocessing script. Explain what it does and identify possible failure points.
```

```text
This notebook cell fails after restarting the kernel. Which variables or imports might be missing?
```

```text
Suggest a smaller test case for this raster workflow before I run it on the full dataset.
```

```text
Help me turn this repeated notebook code into a small function. Keep the code readable for a student project.
```

### Evaluate AI-generated advice

Before applying AI suggestions, ask:

- Does the suggestion match the actual error message?
- Does it use packages that are installed in `sds320`?
- Does it change the project question or method without reason?
- Does it introduce absolute paths?
- Does it hide the problem instead of explaining it?
- Can I explain the suggested change in my own words?
- Did I test the change on a small example first?

```{warning}
AI-generated code can run and still be wrong. In spatial data science, you still need to check CRS, spatial overlap, units, data quality, model assumptions and visual plausibility.
```

---

### How to ask a good question

Use this template when asking for help from instructors, peers or AI tools:

```text
Page or task:
What I tried:
What I expected:
What happened:
Full error message:
Code snippet or screenshot:
Operating system:
Active environment or kernel:
What I already checked:
```

Example:

```text
Page or task: Software setup, package check
What I tried: I activated sds320 and ran the package check in VS Code.
What I expected: GeoPandas should import.
What happened: ModuleNotFoundError in the notebook, but the import works in the terminal.
Full error message: ModuleNotFoundError: No module named 'geopandas'
Operating system: macOS
Active environment or kernel: Python (sds320)
What I already checked: conda info --envs shows sds320 is active in the terminal.
```

This question gives others enough information to suggest the likely cause: the notebook probably uses a different kernel.

---

## Flags & checks

Use this table before changing many things.

| Red flag | First check |
| --- | --- |
| `ModuleNotFoundError` in a notebook | Check the selected Kernel. |
| Package works in terminal but not in notebook | Compare `sys.executable` in notebook and terminal. |
| Python cannot find a file | Print `Path.cwd()` and check whether the path exists. |
| Output files appear in unexpected folders | Check working directory and output paths. |
| A map appears in the wrong location | Check CRS before continuing. |
| Spatial join returns no matches | Check CRS, geometry validity and spatial overlap. |
| Raster processing crashes the kernel | Test on one band, one tile or a smaller area. |
| Model validation looks unrealistically good | Check for data leakage and spatially similar train/test data. |
| AI suggests many code changes at once | Ask for a step-by-step diagnosis instead. |
| You cannot explain the fix | Do not submit it yet; test and understand it first. |
| You solved the error but forgot how | Add a short note to your notebook or README. |

---

## Mini task

Take one previous error, warning or confusing output from your own Python work.

Create a short debugging report using this structure:

```text
Problem title:
Context:
Code or command:
Error message:
Likely cause:
Checks already done:
Next check:
Possible fix:
What I learned:
```

Then do one of the following:

- use it as a prompt for an AI tool,
- save it in your project notes,
- turn it into a short comment or explanation in your notebook.

```{tip}
The goal is not only to fix one error. The goal is to build a troubleshooting habit that makes your project more reproducible.
```

---

## Key takeaways

- Errors are normal in spatial data science projects.
- Debug one problem at a time.
- Check the active environment, kernel and file paths early.
- Use smaller examples before running large workflows.
- Spatial outputs need conceptual checks, not only code checks.
- AI tools can help with debugging, but you remain responsible for testing and understanding the fix.
- Ask questions with enough context for others to help.

---

### What to do next

After this page:

- return to [Software setup](01_software.md) if the problem concerns environments or packages,
- return to [Python reactivation](02_python-reactivation.md) if the problem concerns paths, variables or functions,
- return to [Notebooks and scripts](03_notebooks-scripts.md) if the problem concerns notebook order or hidden state,
- return to [Git basics](04_git-basics.md) if the problem concerns commits, remotes or merge conflicts,
- use [Reproducibility](../03_project/06_reproducibility.md) to document fixes that matter for your final project.
