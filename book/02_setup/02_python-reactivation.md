---
site:
  outline_maxdepth: 2
---

# Python reactivation

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A short refresher for getting back into Python
</div>
<!-- markdownlint-enable MD033 -->

---

## Why this refresher matters

You have already used Python in SDS210. Still, it is normal to feel rusty after a break.

This page helps you reactivate the Python patterns that matter most for SDS320 project work: paths, variables, lists, dictionaries, functions, imports, loops, tables and small workflow checks.

The goal is not to repeat a full Python course. The goal is to help you write code that supports a clear spatial data science workflow instead of isolated code fragments that are difficult to rerun later.

---

## Before you start

Before working through this page, check that you have:

- created the recommended `sds320/` folder structure,
- created and activated the `sds320` {term}`Conda Environment`,
- opened VS Code,
- selected the correct {term}`Jupyter Kernel`,
- created a notebook for this refresher, for example `sds320/course/notebooks/python_reactivation.ipynb`.

```{tip}
Use this page actively. Run the examples, adapt variable names, and connect the patterns to your (upcoming) own project idea (if already applicable).
```

---

## The project coding pathway

A useful SDS320 coding workflow often follows this pathway:

```text
define paths and project settings
→ load or create input data
→ check what you loaded
→ process one small example
→ turn repeated logic into a function
→ apply the function to several files or cases
→ save outputs clearly
→ document what the code does
```

You do not need to write perfect code from the beginning. Start with readable code that you understand, then improve the structure when patterns repeat.

---

### 1. Start with project settings

A good project notebook usually begins with a small setup block. This keeps important project settings visible in one place.

Run this example from a notebook inside a course or project folder.

```{code-cell} python
from pathlib import Path

PROJECT_DIR = Path.cwd()

DATA_DIR = PROJECT_DIR / "data"
RAW_DATA_DIR = DATA_DIR / "raw"
PROCESSED_DATA_DIR = DATA_DIR / "processed"
RESULTS_DIR = PROJECT_DIR / "results"
FIGURES_DIR = RESULTS_DIR / "figures"

STUDY_AREA = "Zurich"
TARGET_CRS = "EPSG:2056"

print(f"Project directory: {PROJECT_DIR}")
print(f"Study area: {STUDY_AREA}")
print(f"Target CRS: {TARGET_CRS}")
```

If you already know your {term}`Coordinate Reference System (CRS)`, storing it as a variable helps you use it consistently later.

Create folders only when your workflow needs to write outputs:

```{code-cell} python
PROCESSED_DATA_DIR.mkdir(parents=True, exist_ok=True)
FIGURES_DIR.mkdir(parents=True, exist_ok=True)

print(PROCESSED_DATA_DIR.exists())
print(FIGURES_DIR.exists())
```

```{warning}
Check what `PROJECT_DIR` prints. If it points to an unexpected folder, your code may save outputs in the wrong place.
```

---

### 2. Variables and strings

Variables store values so that you can reuse them.

```{code-cell} python
study_area = "Zurich"
year = 2026
buffer_distance_m = 100

print(study_area, year, buffer_distance_m)
```

Use meaningful names. A variable called `input_path` is easier to understand than `x`.

Strings are text values. File paths, dataset names, CRS codes and column names are often stored as strings.

```{code-cell} python
dataset_name = "sentinel2"
output_suffix = "ndvi"

output_name = f"{study_area.lower()}_{year}_{dataset_name}_{output_suffix}.tif"
print(output_name)
```

---

### 3. Lists

Lists store multiple values in order.

```{code-cell} python
bands = ["red", "green", "blue", "nir"]

for band in bands:
    print(f"Processing {band}")
```

Lists are useful when you want to process several files, years, classes, image {term}`bands <Band>` or raster {term}`tiles <Tile>`.

```{code-cell} python
years = [2020, 2021, 2022, 2023]

for year in years:
    print(f"Prepare analysis for {year}")
```

---

### 4. Dictionaries

Dictionaries store named values. They are useful for project parameters because they keep related settings together.

```{code-cell} python
project_config = {
    "study_area": "Zurich",
    "start_year": 2020,
    "end_year": 2026,
    "target_crs": "EPSG:2056",
    "data_source": "Sentinel-2",
}

print(project_config["study_area"])
print(project_config["target_crs"])
```

A dictionary can make your code easier to adapt when you change study area, year range or data source.

```{code-cell} python
for key, value in project_config.items():
    print(f"{key}: {value}")
```

---

### 5. Functions

Functions make code reusable. They are useful when the same logic appears more than once.

```{code-cell} python
def build_output_name(study_area, year, suffix, file_extension="tif"):
    """Create a consistent output file name."""
    clean_name = study_area.lower().replace(" ", "_")
    return f"{clean_name}_{year}_{suffix}.{file_extension}"


filename = build_output_name("Zurich", 2026, "ndvi")
print(filename)
```

Functions help you follow the {abbr}`DRY (Don't Repeat Yourself)` principle: avoid copying the same code again and again when one reusable function would be clearer.

A small helper function can also make file checks more readable:

```{code-cell} python
def check_path(path):
    """Print whether a file or folder exists."""
    path = Path(path)

    if path.exists():
        print(f"Found: {path}")
    else:
        print(f"Missing: {path}")


check_path(DATA_DIR)
check_path(RAW_DATA_DIR)
```

---

### 6. Imports

Imports load packages or modules.

```{code-cell} python
from pathlib import Path

import pandas as pd
```

Keep imports near the top of notebooks and scripts. This makes dependencies visible.

For spatial projects, you will often import packages such as {term}`GeoPandas`, Rasterio, Leafmap or PyTorch. Only import them when you need them.

```python
import geopandas as gpd
import rasterio
import leafmap
import torch
```

This example is illustrative. It assumes that the packages are installed in the active environment.

---

### 7. Paths and working directories

Paths are central in project work. Use `pathlib` to make paths clearer and more portable.

```{code-cell} python
example_path = DATA_DIR / "raw" / "example_file.tif"

print(example_path)
print(example_path.exists())
```

Your {term}`Working Directory` is the folder from which Python currently runs. Many file errors happen because the working directory is different from what you expect.

```{code-cell} python
print(Path.cwd())
```

```{warning}
Avoid hard-coded absolute paths such as `/Users/name/Desktop/project/data/file.tif` or `C:\Users\name\Desktop\project\data\file.tif`. They usually break on another computer.
```

Prefer paths that are relative to your project folder.

---

### 8. Conditionals

Conditionals let your code react to situations.

```{code-cell} python
if RAW_DATA_DIR.exists():
    print("Raw data folder exists.")
else:
    print("Raw data folder is missing.")
```

You will often use conditionals for checks, such as whether a file exists, whether a dataset has the expected columns, or whether an output should be created.

```{code-cell} python
required_columns = ["dataset", "type"]
available_columns = ["dataset", "type", "provider"]

for column in required_columns:
    if column in available_columns:
        print(f"Found column: {column}")
    else:
        print(f"Missing column: {column}")
```

---

### 9. DataFrames

A DataFrame is a table-like data structure. It is useful for organising dataset inventories, project parameters, evaluation results or summary statistics.

```{code-cell} python
import pandas as pd

data = {
    "dataset": ["Landsat", "OpenStreetMap", "SwissBOUNDARIES3D"],
    "type": ["raster", "vector", "vector"],
    "status": ["possible", "possible", "check licence"],
}

df = pd.DataFrame(data)
df
```

Inspect the table before using it in a longer workflow:

```{code-cell} python
print(df.columns)
print(df.head())
```

You can filter rows to focus on a subset:

```{code-cell} python
vector_data = df[df["type"] == "vector"]
vector_data
```

---

### 10. GeoDataFrames

A {term}`GeoDataFrame` is like a DataFrame, but with a geometry column. It is used for {term}`Vector Data`, such as points, lines or polygons.

```python
import geopandas as gpd

gdf = gpd.read_file("data/raw/vectors/study_area.gpkg")
gdf.head()
```

This example is not executable unless the file exists in your project.

When working with a GeoDataFrame, useful first checks include:

```python
print(gdf.crs)
print(gdf.shape)
print(gdf.columns)
gdf.plot()
```

These checks help you understand the spatial reference, number of features, attributes and approximate geometry.

---

### 11. Raster data reminders

{term}`Raster Data` are stored as grids of pixels or cells. Satellite images, elevation models, masks and prediction maps are common raster datasets.

A typical raster workflow includes:

```text
open raster
→ inspect metadata
→ read selected band or window
→ check values and nodata
→ process array
→ save output with spatial metadata
```

This page does not introduce raster processing in detail. The important reactivation idea is: do not trust a raster just because it loads. Check shape, CRS, bounds, resolution and value range before using it in analysis or modelling.

```python
import rasterio

with rasterio.open("data/raw/raster/example.tif") as src:
    print(src.crs)
    print(src.bounds)
    print(src.width, src.height)
    print(src.count)
```

This example is illustrative and requires an existing raster file.

---

### 12. From fragments to a pipeline

In SDS320, Python should help you build a repeatable {term}`Data Pipeline`.

A simple project pipeline might look like this:

```text
define paths
→ load data
→ check data
→ preprocess data
→ run method
→ evaluate output
→ create figure
→ save result
```

Try to make these steps visible in your notebooks and scripts. Clear sections, meaningful variable names and small helper functions make it easier to understand your own work later.

```{tip}
Before writing more code, ask: “Would I understand this workflow again in two weeks?” If the answer is no, add structure now.
```

For the next step, see [Notebooks and scripts](03_notebooks-scripts.md), where you decide which parts of your workflow should remain in notebooks and which parts could become reusable scripts.

---

## Flags & checks

Use this table when your Python workflow becomes confusing.

| Red flag | First check |
| --- | --- |
| A package imports in the terminal but not in the notebook | Check the selected Jupyter kernel. |
| Python cannot find a file | Print `Path.cwd()` and check whether the path exists. |
| Output files appear in an unexpected folder | Check `PROJECT_DIR` and your working directory. |
| The same code appears in many cells | Turn repeated logic into a function. |
| A notebook only works when cells are run in a special order | Restart the kernel and run all cells from top to bottom. |
| A file path includes your personal user folder | Replace it with a project-relative path. |
| A GeoDataFrame plots in the wrong location | Check the CRS before further processing. |
| A raster loads but looks strange | Check band order, nodata values, CRS, shape and value range. |
| Your notebook is difficult to understand after a week | Add Markdown headings, comments and clearer variable names. |

For more help, see [Troubleshooting](05_troubleshooting.md).

---

## Mini task

Create or update a notebook called:

```text
sds320/course/notebooks/python_reactivation.ipynb
```

Complete the following steps:

- [ ] Define `PROJECT_DIR`, `DATA_DIR`, `RAW_DATA_DIR`, `PROCESSED_DATA_DIR`, `RESULTS_DIR` and `FIGURES_DIR`.
- [ ] Print the current working directory.
- [ ] Create a `project_config` dictionary with study area, target CRS, data source and year range.
- [ ] Write a function called `check_path()` that reports whether a file or folder exists.
- [ ] Use a loop to check at least three project folders.
- [ ] Create a small DataFrame that lists three possible datasets for your project.
- [ ] Add one Markdown cell explaining which part of your current project workflow is still unclear.

```{tip}
The goal is not to produce final project code. The goal is to reactivate the habits you will need later: define paths, check inputs, organise settings, write small functions and document decisions.
```

---

## Key takeaways

- You do not need to remember every Python detail immediately.
- Focus on patterns that support project work: paths, imports, functions, loops, checks and small tables.
- Keep project settings visible near the top of notebooks and scripts.
- Use project-relative paths instead of personal absolute paths.
- Turn repeated code into small functions.
- Check intermediate outputs before trusting final results.
- Clear Python habits support {term}`Reproducibility`.

---

### What to do next

After this refresher:

- use [Notebooks and scripts](03_notebooks-scripts.md) to decide how to organise your code,
- use [Git basics](04_git-basics.md) to start tracking meaningful changes,
- use [Workflow design](../03_project/04_workflow-design.md) to connect code steps to your project question,
- use [Reproducibility](../03_project/06_reproducibility.md) to check whether someone else could rerun your work.
