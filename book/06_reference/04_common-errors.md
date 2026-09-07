---
site:
  outline_maxdepth: 1
---

# Common errors

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Potential project problems and how to approach them
</div>
<!-- markdownlint-enable MD033 -->

---

## How to use this page

When something fails, avoid changing several things at once.

A useful debugging sequence is:

```text
What failed?
      ↓
Where did it fail?
      ↓
What did I expect?
      ↓
What object or value actually reached that step?
      ↓
What is the smallest check I can run?
```

Start with the error message and the line that failed. Then inspect inputs, assumptions and intermediate outputs.

For a fuller debugging workflow, see [Troubleshooting](../02_setup/05_troubleshooting.md).

---

## Environment and package errors

| Symptom | Likely cause | First checks |
| --- | --- | --- |
| `ModuleNotFoundError` | Package is not installed in the active environment | Check active environment and notebook kernel |
| Import works in Terminal but not Jupyter | Terminal and notebook use different Python environments | Inspect the selected Jupyter kernel |
| Package API differs from course example | Different package version | Check package version and current documentation |
| Environment creation fails | Dependency conflict or incomplete environment file | Read the complete solver/error output |
| GPU is not detected | Hardware, CUDA or environment configuration differs | Check whether PyTorch detects CUDA and whether GPU use is actually required |

Check the Python executable:

```python
import sys

print(sys.executable)
```

Inspect a package version:

```python
import geopandas as gpd

print(gpd.__version__)
```

```{tip}
If an import works in one place but not another, compare the Python executable used in both environments before reinstalling packages.
```

---

## Jupyter kernel errors

A {term}`Jupyter Kernel` is the Python process that executes notebook code.

### Wrong kernel

Symptoms:

- imports unexpectedly fail,
- package versions differ,
- code works in Terminal but not in the notebook.

Check:

```python
import sys

sys.executable
```

Make sure it belongs to the expected SDS320 environment.

### Stale notebook state

A notebook may depend on variables created by cells that are no longer visible or have been run in a different order.

Typical warning signs:

- a variable exists even though its defining cell was not run,
- cells fail when executed from top to bottom,
- restarting Jupyter changes the result.

Run a {term}`Restart and Run All` check regularly.

### Kernel crash

Possible causes include:

- running out of memory,
- loading a very large raster,
- GPU-memory exhaustion,
- problematic native libraries.

Reduce the data or model size before assuming the notebook itself is broken.

---

## Path and file errors

### `FileNotFoundError`

First check:

```python
from pathlib import Path

path = Path("data/image.tif")

print(path)
print(path.exists())
```

Then inspect:

```python
Path.cwd()
```

Common causes include:

- incorrect relative path,
- incorrect filename,
- unexpected capitalization,
- wrong {term}`Working Directory`,
- file not downloaded,
- file stored outside the repository.

### Absolute paths

An {term}`Absolute Path` such as:

```text
/Users/name/Documents/SDS320/data/image.tif
```

works only on a particular filesystem.

Prefer project-relative paths where possible:

```text
data/image.tif
```

### Permission errors

If the file exists but cannot be opened, check:

- whether you have read/write permission,
- whether another application has locked it,
- whether the target directory is writable.

Do not solve ordinary project-path problems by broadly changing system permissions.

---

## Vector data errors

### CRS mismatch

Symptoms:

- layers appear in different locations,
- spatial joins return unexpectedly few matches,
- distances or areas look implausible.

Check:

```python
print(gdf1.crs)
print(gdf2.crs)
```

A {term}`Coordinate Reference System` defines how coordinates relate to Earth.

Reproject deliberately when needed:

```python
gdf2 = gdf2.to_crs(gdf1.crs)
```

Do not simply assign a new CRS to coordinates that are actually stored in another CRS.

### Invalid geometries

Symptoms:

- overlay or intersection operations fail,
- spatial operations return unexpected geometries.

Inspect:

```python
gdf.geometry.is_valid.value_counts()
```

Understand what makes the geometry invalid before applying an automated repair.

### Missing attributes

Inspect the available columns:

```python
gdf.columns
```

Common causes include:

- attribute renamed during preprocessing,
- wrong input dataset,
- merge or join changed the column names.

### Spatial join returns no matches

Check:

1. both CRS,
2. geographic overlap,
3. geometry types,
4. spatial predicate,
5. a small map of both layers.

```{tip}
Plot the two datasets together before debugging a complicated spatial operation. Many spatial problems become obvious once you see the inputs.
```

---

## Raster data errors

### Wrong band order

Symptoms:

- RGB imagery looks strangely coloured,
- a model performs unexpectedly poorly,
- training and inference appear inconsistent.

Inspect the raster metadata and document the {term}`Band Order`.

Do not infer band meaning only from the number of channels.

### NoData treated as real data

A {term}`NoData Value` may represent:

- outside-scene pixels,
- missing observations,
- masked clouds,
- invalid measurements.

Check:

```python
with rasterio.open(path) as src:
    print(src.nodata)
```

### Raster and vector do not align

Check:

- CRS,
- bounds,
- raster transform,
- pixel size,
- acquisition date where relevant.

A correct CRS alone does not guarantee pixel-level alignment.

### Memory errors

Avoid immediately loading a very large raster into memory.

Instead consider:

- reading a window,
- clipping a preview area,
- processing tiles,
- using chunked tools where appropriate.

```python
with rasterio.open(path) as src:
    print(src.width, src.height)
    print(src.count)
    print(src.dtypes)
```

Estimate the scale of the data before reading all bands.

### Lost spatial metadata

A NumPy array contains pixel values but not automatically:

- CRS,
- affine transform,
- geographic extent,
- NoData metadata.

Preserve the input raster profile when writing geospatial outputs.

---

## Machine learning workflow errors

### Image-label shape mismatch

Symptoms:

- training fails during batching,
- array dimensions differ,
- masks are offset or resized incorrectly.

Check several image-label pairs explicitly:

```text
image width/height
mask width/height
number of channels
mask class values
```

For segmentation, visually overlay labels on imagery.

### Data leakage

{term}`Data Leakage` occurs when information from validation or test data influences training.

Common spatial causes include:

- overlapping tiles in training and validation,
- neighbouring chips from the same scene,
- duplicated samples,
- fitting preprocessing using all data.

If validation performance appears surprisingly strong, inspect the split before tuning the model.

### Class imbalance

A {term}`Class Imbalance` problem occurs when some classes dominate the training data.

Symptoms can include:

- high overall accuracy,
- poor results for rare classes,
- model predicting the majority class repeatedly.

Inspect class frequencies and class-specific metrics.

### Overfitting

{term}`Overfitting` occurs when training performance continues improving while performance on unseen data stops improving or deteriorates.

Possible responses include:

- more representative training data,
- stronger data separation,
- reducing model complexity,
- regularization,
- early stopping,
- revisiting the number of training epochs.

Do not automatically assume that more training will solve poor validation performance.

### Training and inference configuration differ

Check that both stages use compatible:

- model architecture,
- checkpoint,
- number of classes,
- channel count,
- band order,
- normalization,
- tile/window configuration where required.

### Validation looks good but project predictions look poor

Possible explanations include:

- spatially weak validation split,
- different sensor,
- different season,
- different geographic region,
- different spatial resolution,
- insufficient training variation.

This is a {term}`Model Generalization` problem rather than necessarily a coding error.

---

## Git and repository errors

### Files are missing from the repository

Run:

```bash
git status
```

A file may be:

- untracked,
- ignored by `.gitignore`,
- created outside the repository.

### Large files are being tracked

Raw raster datasets and model outputs can make a repository difficult or impossible to push.

Check before committing:

```bash
git status
```

Keep large project data outside Git unless the course workflow explicitly requires otherwise.

Document how to obtain the data in the README.

### Merge conflict

A {term}`Merge Conflict` means Git cannot automatically combine two versions of a file.

Do not delete conflict markers blindly.

First identify:

```text
<<<<<<<
=======
>>>>>>>
```

Then decide which content should remain, edit the file and test it before committing the resolution.

### Repository works only on your computer

Check for:

- absolute paths,
- missing environment information,
- files that were never committed,
- hidden notebook state,
- undocumented downloads,
- machine-specific settings.

Use the [Reproducibility](../03_project/06_reproducibility.md) and [Repository](../03_project/07_repository.md) pages for the fuller project requirements.

---

## Conceptual errors that look technical

Not every unexpected output is a software problem.

Examples include:

| Symptom | Possible conceptual cause |
| --- | --- |
| Buildings are consistently missed | Image resolution may be too coarse |
| Change map highlights many unchanged areas | Seasonal or radiometric differences |
| Segmentation boundaries are shifted | Reference labels may be misaligned |
| Model works only in one region | Weak geographic generalization |
| Spatial join returns surprising matches | Wrong spatial predicate |
| High accuracy but poor minority class | Class imbalance |
| Visually smooth prediction but poor validation | Attractive output is not necessarily accurate |

Before changing code, ask whether the input data and method are capable of answering the intended question.

---

## How to ask for help

When asking a tutor, classmate or AI assistant for help, provide enough information to reproduce the problem.

Use:

```text
Page or task:
What I tried:
What I expected:
What happened:
Full error message:
Relevant code snippet or screenshot:
What I already checked:
```

For Python errors, include the **complete traceback**, especially the final lines.

```{important}
Never include passwords, API keys, tokens or other private credentials in a help request, screenshot or repository.
```

A {term}`Small Reproducible Example` is often the fastest way to turn a large project problem into something that can actually be diagnosed.

---

## Key takeaways

- Read the complete error message before changing code.
- Check inputs and intermediate outputs rather than only the final failing line.
- Verify the active environment and Jupyter kernel early.
- Many geospatial errors come from CRS, alignment, resolution, NoData or band assumptions.
- Many model problems come from data and validation design rather than the architecture.
- Change one thing at a time and test the smallest relevant part of the workflow.
