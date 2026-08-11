---
site:
 outline_maxdepth: 1
 
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

This page is a short reactivation. It does not replace a full Python course. It reminds you of patterns that are especially useful in SDS320: paths, lists, dictionaries, functions, imports, loops, DataFrames and small reusable workflow steps.

The goal is to help you write code that supports your project, not isolated code fragments that are difficult to rerun later.

---

## What you should remember from SDS210

### Variables

Variables store values so that you can reuse them.

```{code-cell} python
study_area = "Zurich"
year = 2026
buffer_distance_m = 100

print(study_area, year, buffer_distance_m)
```

Use meaningful names. A variable called `input_path` is easier to understand than `x`.

### Lists

Lists store multiple values in order.

```{code-cell} python
bands = ["red", "green", "blue", "nir"]

for band in bands:
    print(f"Processing {band}")
```

Lists are useful when you want to process several files, bands, classes or years.

### Dictionaries

Dictionaries store named values.

```{code-cell} python
project_config = {
    "study_area": "Zurich",
    "start_year": 2020,
    "end_year": 2026,
    "target_crs": "EPSG:2056",
}

print(project_config["target_crs"])
```

Dictionaries are useful for project parameters because they keep related settings together.

### Functions

Functions make code reusable.

```{code-cell} python
def build_output_name(study_area, year, suffix):
    """Create a consistent output file name."""
    clean_name = study_area.lower().replace(" ", "_")
    return f"{clean_name}_{year}_{suffix}.tif"


filename = build_output_name("Zurich", 2026, "ndvi")
print(filename)
```

If you repeat the same logic several times, consider turning it into a function.

### Imports

Imports load packages or modules.

```{code-cell} python
from pathlib import Path

import pandas as pd
import geopandas as gpd
```

Keep imports near the top of notebooks and scripts. This makes dependencies visible.

### Paths

Paths are central in project work. Use `pathlib` to make paths clearer and more portable.

```{code-cell} python
from pathlib import Path

project_dir = Path.cwd()
data_dir = project_dir / "data"
output_dir = project_dir / "outputs"

print(data_dir)
print(output_dir)
```

Create folders when needed:

```{code-cell} python
output_dir.mkdir(exist_ok=True)
```

```{warning}
Avoid hard-coded absolute paths such as `/Users/name/Desktop/project/data/file.tif`. They usually break on another computer.
```

### Loops

Loops repeat an action.

```{code-cell} python
years = [2020, 2021, 2022, 2023]

for year in years:
    output_name = build_output_name("Zurich", year, "summary")
    print(output_name)
```

Loops are useful for processing multiple years, tiles, images or administrative units.

### Conditionals

Conditionals let your code react to situations.

```{code-cell} python
file_exists = True

if file_exists:
    print("Continue with processing.")
else:
    print("Download or create the missing file first.")
```

You will often use conditionals for checks, such as whether a file exists or whether a dataset has the expected columns.

### DataFrames

A DataFrame is a table-like data structure.

```{code-cell} python
import pandas as pd

data = {
    "dataset": ["Landsat", "OpenStreetMap", "SwissBOUNDARIES3D"],
    "type": ["raster", "vector", "vector"],
}

df = pd.DataFrame(data)
df
```

You can inspect rows, columns and basic information:

```{code-cell} python
print(df.columns)
print(df.head())
```

### GeoDataFrames

A GeoDataFrame is like a DataFrame, but with a geometry column.

```python
import geopandas as gpd

gdf = gpd.read_file("data/study_area.gpkg")
gdf.head()
```

This example is not executable unless the file exists in your project.

---

## Small examples

### A small project parameter block

At the beginning of a notebook, define key project settings in one place.

```python
from pathlib import Path

PROJECT_DIR = Path.cwd()
DATA_DIR = PROJECT_DIR / "data"
OUTPUT_DIR = PROJECT_DIR / "outputs"

STUDY_AREA = "Zurich"
TARGET_CRS = "EPSG:2056"

OUTPUT_DIR.mkdir(exist_ok=True)

print(f"Project directory: {PROJECT_DIR}")
print(f"Study area: {STUDY_AREA}")
```

This makes your notebook easier to adapt and review.

### A helper function for checking files

```python
from pathlib import Path

def check_file(path):
    """Print whether a file exists."""
    path = Path(path)

    if path.exists():
        print(f"Found: {path}")
    else:
        print(f"Missing: {path}")


check_file("data/example_file.tif")
```

This small function does not solve all file problems, but it encourages a useful habit: check inputs before running long workflows.

### Looping through possible input files

```python
input_files = [
    "data/image_2020.tif",
    "data/image_2021.tif",
    "data/image_2022.tif",
]

for input_file in input_files:
    check_file(input_file)
```

This pattern becomes useful when working with multiple scenes, years or tiles.

---

## Thinking in project workflows

In SDS320, Python should help you build repeatable project steps.

A project workflow might look like this:

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

```{admonition} Reminder
:class: tip
The course environment file provided for SDS320 is the main reference for package installation. It uses the environment name `sds320` and Python 3.12.

```

---

## Common pitfalls

### Forgetting which environment is active

A notebook may use a different Python environment than your terminal. If imports fail unexpectedly, check the selected kernel.

### Confusing strings and variables

```{code-cell} python
data_dir = "data"
print(data_dir)      # prints the value of the variable
print("data_dir")    # prints the text data_dir
```

This small difference can cause path and file-name confusion.

### Hard-coding paths

Hard-coded paths work on one machine but usually fail elsewhere. Prefer project-relative paths with `pathlib`.

### Writing very long notebooks without functions

Long notebooks can become difficult to rerun. If a cell becomes too long or repeated, consider writing a function. Mind the {abbr}`DRY (Don't Repeat Yourself)` principle.

### Not checking intermediate outputs

Spatial workflows can run without obvious errors but still produce wrong results. Check maps, tables, shapes, coordinate reference systems and value ranges regularly.

---

## Mini task

Create a small project setup block for your own SDS320 project.

It should include:

- project directory,
- data directory,
- output directory,
- study area,
- target coordinate reference system, if known,
- one helper function that checks whether a file exists.

Use this as the first draft of the setup section in your project notebook.

---

## Key takeaways

- You do not need to remember every Python detail immediately.
- Focus on patterns that support project work: paths, imports, functions, loops and checks.
- Use meaningful variable names and small reusable functions.
- Keep project settings visible near the top of notebooks and scripts.
- Check intermediate outputs before trusting final results.