---
site:
 outline_maxdepth: 2
---

<!-- Add Troubleshooting with a LLM or Agent-->

# Troubleshooting

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A practical guide for when your code, environment or data workflow breaks
</div>
<!-- markdownlint-enable MD033 -->

---

## Why troubleshooting matters

Errors are normal in spatial data science. They are not a sign that you are bad at programming.

SDS320 projects combine Python, packages, notebooks, spatial data, file paths, repositories and sometimes machine learning libraries. Many things can break. Good troubleshooting helps you find the likely cause step by step instead of changing many things at once.

Troubleshooting is also part of reproducible project work. If you understand why something failed, you can document the fix and avoid the same problem later.

## First response to an error

When something breaks, use this routine.

### 1. Read the error message

Start at the bottom of the error message. Python usually shows the most direct error there.

Look for:

- the error type,
- the file or notebook cell,
- the line that failed,
- the object or package mentioned.

### 2. Identify the line that failed

Do not debug the whole notebook at once. Find the specific line that caused the error.

### 3. Check the active environment

Many package errors come from using the wrong Python environment.

In the terminal:

```bash
conda info --envs
```

In Python:

```{code-cell} python
import sys

print(sys.executable)
```

Check whether the path points to your `sds320` environment.

### 4. Check file paths

Many spatial data workflows fail because a file path is wrong.

```{code-cell} python
from pathlib import Path

path = Path("data/example.tif")

print(Path.cwd())
print(path.exists())
```

If `path.exists()` returns `False`, Python cannot find the file from the current working directory.

### 5. Run a smaller example

If a workflow fails on a large dataset, test the same logic on a smaller file, fewer rows, one tile or one band.

### 6. Search course materials or documentation

Check the relevant course page first. Then use official package documentation if needed.

### 7. Ask a clear question

If you are still stuck, ask on MS Teams with enough information for others to help.

---

## Common error categories

### Environment and package errors

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

### Jupyter kernel errors

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

Then restart JupyterLab.

Also try:

```text
Kernel → Restart Kernel and Run All Cells
```

This reveals hidden-state problems.

### Path and file errors

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

```{tip}
When debugging paths, print the path and check whether it exists before passing it into a longer workflow.
```

### CRS and spatial data errors

Common symptoms:

- layers do not overlap on a map,
- spatial joins return no matches,
- distances or areas look wrong,
- maps appear in unexpected locations.

Likely causes:

- missing {abbr}`CRS (Coordinate Reference System)`,
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

### Raster size or memory errors

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
- save intermediate outputs.

### Machine learning workflow errors

Common symptoms:

- shape mismatch errors,
- labels and images do not align,
- training runs but results are meaningless,
- validation performance is unrealistically high.

Likely causes:

- input arrays have unexpected dimensions,
- labels use a different resolution or extent,
- train and test data are spatially too similar,
- classes are strongly imbalanced,
- preprocessing differs between training and prediction.

First checks:

- print array shapes,
- plot image and label together,
- inspect class counts,
- check train/validation/test split logic,
- test the workflow on a few samples first.

### Git errors

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

---

## How to ask a good question

Use this template when asking for help:

```text
Page or task:
What I tried:
What I expected:
What happened:
Full error message:
Code snippet or screenshot:
What I already checked:
```

Example:

```text
Page or task: Software setup, package check
What I tried: I activated sds320 and ran the package check in JupyterLab.
What I expected: GeoPandas should import.
What happened: ModuleNotFoundError in the notebook, but the import works in the terminal.
Full error message: ModuleNotFoundError: No module named 'geopandas'
What I already checked: conda info --envs shows sds320 is active in the terminal.
```

This question gives others enough information to suggest the likely cause: the notebook probably uses the wrong kernel.

---

## What not to do

Avoid these troubleshooting habits:

- sending only “it does not work”,
- changing many things at once,
- reinstalling packages without checking the active environment,
- ignoring the first error and focusing only on later errors,
- pasting screenshots without the actual error text,
- continuing with suspicious outputs because there was no Python error.

A workflow can run without crashing and still be wrong. Visual and conceptual checks matter.

---

## Mini task

Take one previous error, warning or confusing output from your own Python work.

Rewrite it as a useful help request using the template above. Include what you tried, what happened and what you already checked.

---

## Key takeaways

- Errors are normal in spatial data science projects.
- Debug one problem at a time.
- Check the active environment and file paths early.
- Restart and rerun notebooks to detect hidden-state problems.
- Use small examples before running large workflows.
- Ask questions with enough context for others to help.
