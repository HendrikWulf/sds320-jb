---
site:
  outline_maxdepth: 2
---

# Cheatsheets

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Small reminders for Python, notebooks and spatial data workflows
</div>
<!-- markdownlint-enable MD033 -->

---

## How to use these cheatsheets

These are reminders, not tutorials. Each snippet assumes you already understand the underlying concept from an earlier lesson and just need to recall the exact syntax. If a pattern here does not make sense on its own, go back to the lesson that introduced it rather than trying to learn it from scratch here.

---

## Python basics

```python
# Variables
tile_size = 256
class_name = "building"

# Lists
class_names = ["background", "building", "road"]
first_class = class_names[0]

# Dictionaries
metrics = {"iou": 0.82, "f1": 0.90}
print(metrics["iou"])

# Loops
for name in class_names:
    print(name)

# Functions
def to_hectares(area_m2):
    return area_m2 / 10_000

# Imports
import geoai
import numpy as np
from pathlib import Path

# Paths
data_path = Path("data") / "raw" / "image.tif"
```

---

## DataFrames and GeoDataFrames

```python
import pandas as pd
import geopandas as gpd

# Reading data
df = pd.read_csv("results.csv")
gdf = gpd.read_file("buildings.geojson")

# Inspecting columns
print(df.columns)
print(gdf.head())

# Filtering rows
large_buildings = gdf[gdf["area_m2"] > 100]

# Selecting columns
subset = gdf[["geometry", "area_m2", "class"]]

# Plotting a simple output
gdf.plot(column="area_m2", legend=True)

# Saving results
gdf.to_file("filtered_buildings.geojson", driver="GeoJSON")
df.to_csv("summary.csv", index=False)
```

---

## Raster workflows

```python
import rasterio as rio
import numpy as np

# Opening raster data
with rio.open("image.tif") as src:
    # Inspecting metadata
    print(src.count, src.width, src.height, src.crs, src.res)

    # Reading bands
    band_1 = src.read(1)
    rgb = src.read([1, 2, 3])

    # Handling nodata values
    nodata_value = src.nodata
    valid = band_1 != nodata_value

# Plotting an array
import matplotlib.pyplot as plt
plt.imshow(band_1, cmap="viridis")
plt.colorbar()
plt.show()

# Saving outputs (matching the input's profile)
with rio.open("image.tif") as src:
    profile = src.profile

with rio.open("output.tif", "w", **profile) as dst:
    dst.write(band_1, 1)
```

---

## Project paths and folders

```python
from pathlib import Path

project_root = Path.cwd()
data_dir = project_root / "data"
output_dir = project_root / "outputs"

output_dir.mkdir(exist_ok=True)

# Build a path without worrying about slashes
image_path = data_dir / "raw" / "scene_2024.tif"
```

```{tip}
Prefer relative paths built with `pathlib` over hardcoded absolute paths. A notebook with `Path("data/scene.tif")` runs on any machine; one with `"C:/Users/yourname/Desktop/data/scene.tif"` only runs on yours.
```

---

## Figures and maps

```python
fig, ax = plt.subplots(figsize=(8, 6))
ax.imshow(band_1, cmap="viridis")

ax.set_title("Study Area NDVI")   # Title
ax.set_xlabel("Column")            # Labels
ax.set_ylabel("Row")

# Legends (for categorical or vector plots)
gdf.plot(ax=ax, column="class", legend=True)

# Saving a figure
fig.savefig("outputs/ndvi_map.png", dpi=300, bbox_inches="tight")
```

```{tip}
Give every figure you include in a report a caption that states what it shows and, where relevant, the date or source of the data. A reader should be able to understand a figure without re-reading your whole report.
```

---

## Git reminders

```bash
git status
git add .
git commit -m "Add NDVI tiling script"
git push
```

```{admonition} Keep large data out of Git
:class: warning
Do not commit large raster files, model checkpoints, or downloaded datasets to your repository. Use a `.gitignore` file, and document in your README how to obtain the data instead.
```

---

## When to look elsewhere

These cheatsheets are intentionally short. For fuller explanations, revisit:

- the [Python & project setup](../02_setup.md) section for environment and Git basics,
- the specific lesson that introduced a workflow (for example, L04 for tiling, L07 for raster masks),
- the [Project handbook](../03_project.md) for repository and reproducibility expectations.

For a broader set of reference cheatsheets covering `matplotlib`, `numpy`, `pandas`, plain Python, `pytorch`, `scikit-learn`, and `git` individually, see the course's external [cheatsheet collection](https://source.coop/giuz/sds320/Reference/cheatsheets).
