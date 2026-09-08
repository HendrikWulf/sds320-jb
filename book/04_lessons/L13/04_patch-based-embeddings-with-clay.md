---
site:
  outline_maxdepth: 2
---

# Patch-based embeddings

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Generating patch-based embeddings with the Clay foundation model
</div>
<!-- markdownlint-enable MD033 -->

---

You can now find a dataset in the registry. This page downloads and assembles a real one: Clay Foundation Model embeddings for the San Francisco Bay Area, the dataset the rest of this lesson's hands-on pages build on.

---

## 1. Motivation

This is the page where the registry becomes actual data you can analyze. Clay's embeddings, computed from NAIP aerial imagery across 20 tiles, come with labeled points for two land-use classes, which makes this dataset useful for every later page in this lesson, from unsupervised exploration through classification.

---

## 2. Core idea

Clay produces 768-dimensional patch embeddings from a range of satellite and aerial sources. This lesson's dataset is distributed as {term}`GeoParquet` files on {term}`Hugging Face Hub`, one file per tile; the workflow is to download each tile, combine them into a single {term}`GeoDataFrame`, and extract the embedding vectors into a NumPy matrix ready for analysis.

---

## 3. Workflow

### A. List and download embedding tiles

```{code-cell} python
from huggingface_hub import HfApi, hf_hub_download

repo_id = "made-with-clay/classify-embeddings-sf-baseball-marinas"
api = HfApi()
embedding_files = [
    f.path
    for f in api.list_repo_tree(repo_id, repo_type="dataset")
    if f.path.endswith(".gpq")
]

print(f"Found {len(embedding_files)} embedding tiles")
```

`repo_type="dataset"` is important here; without it, `HfApi` looks for a model repository instead of a dataset repository, and the call will not find the files you expect. Each `.gpq` file is one GeoParquet tile of embeddings.

### B. Combine tiles into a GeoDataFrame

```{code-cell} python
import geopandas as gpd
import pandas as pd

all_gdfs = []
for f in embedding_files:
    path = hf_hub_download(repo_id, f, repo_type="dataset")
    gdf = gpd.read_parquet(path)
    all_gdfs.append(gdf)

embeddings_gdf = pd.concat(all_gdfs, ignore_index=True)
embeddings_gdf = gpd.GeoDataFrame(embeddings_gdf, geometry="geometry", crs=all_gdfs[0].crs)

print(f"Combined: {len(embeddings_gdf)} patches")
print(f"Embedding dimension: {len(embeddings_gdf.iloc[0]['embeddings'])}")
```

Each downloaded tile is read directly with `gpd.read_parquet()`, since GeoParquet is a standard, directly readable geospatial format. `pd.concat()` stacks all 20 tiles' rows into one table, and re-wrapping the result as a `GeoDataFrame` (with the first tile's CRS) restores its spatial behavior, since `pd.concat()` alone returns a plain DataFrame.

### C. Download the labeled points

```{code-cell} python
labels_file = hf_hub_download(repo_id, "baseball.geojson", repo_type="dataset")
labels_gdf = gpd.read_file(labels_file)

print(f"Labeled locations: {len(labels_gdf)}")
print(labels_gdf["class"].value_counts())
```

This dataset also ships labeled points for two classes, baseball fields (class 0) and marinas (class 1), which you will use for the classification workflow two pages from now.

### D. Extract the embedding matrix

```{code-cell} python
import numpy as np

embeddings = np.stack(embeddings_gdf["embeddings"].values)
centroids = embeddings_gdf.geometry.centroid
coords_x = centroids.x.values
coords_y = centroids.y.values

print(f"Embeddings shape: {embeddings.shape}")
```

Each row's `embeddings` column holds a 768-element vector; `np.stack()` combines all of them into a single two-dimensional array, one row per patch, ready for the analysis functions used throughout the rest of this lesson. Extracting each patch's centroid coordinates separately is what lets you plot embedding-space results back onto a real geographic map, a pattern you will use repeatedly starting on the next page.

---

## 4. Python reactivation

The list comprehension building `embedding_files` (`[f.path for f in ... if f.path.endswith(".gpq")]`) combines filtering and extraction in one line, the same pattern used for file-path lists throughout this course. `np.stack()` differs from a plain `np.array()` call on a column of arrays: it explicitly combines a sequence of equal-shaped arrays along a new axis, which is the reliable way to turn a pandas column of vectors into a proper two-dimensional matrix.

---

## 5. Common pitfalls

- **Forgetting `repo_type="dataset"`.** Without it, Hugging Face API calls default to looking for a model repository, and will not find dataset files.
- **Concatenating tiles without restoring the GeoDataFrame type and CRS.** `pd.concat()` on GeoDataFrames returns a plain DataFrame; explicitly re-wrapping it, as in step B, is necessary to keep spatial functionality.
- **Using `np.array()` instead of `np.stack()` on a column of vectors.** Depending on the input, this can silently produce an array of arrays rather than the clean two-dimensional matrix downstream functions expect; `np.stack()` avoids this ambiguity.
- **Losing track of which coordinates correspond to which embeddings.** Keep `coords_x`, `coords_y`, and `embeddings` extracted in a consistent order (as done here, all from the same `embeddings_gdf`), since later pages plot and filter these together.

---

## 6. Mini task

`embeddings.shape` prints `(36024, 768)`. Without looking at any other output, explain what each of these two numbers represents.

:::{dropdown} Sample solution
:class: note

36024 is the number of patches (rows), one per image tile combined across all 20 downloaded GeoParquet files. 768 is the embedding dimension (columns), the length of each individual patch's Clay embedding vector, consistent with the registry's reported dimensionality for the Clay dataset from the previous page.
:::

---

## 7. Key takeaways

- Clay embeddings for this lesson's example are distributed as per-tile GeoParquet files on Hugging Face; `repo_type="dataset"` is required to access them correctly.
- Combining tiles with `pd.concat()` requires explicitly re-wrapping the result as a GeoDataFrame to restore spatial behavior.
- `np.stack()` reliably converts a column of embedding vectors into a clean two-dimensional matrix.
- Keeping embeddings and their geographic coordinates aligned is essential for the geographic plotting used throughout the rest of this lesson.

---

:::{admonition} Optional: loading datasets directly with TorchGeo
:class: dropdown

For more advanced use, `geoai.load_embedding_dataset()` loads TorchGeo dataset classes directly, giving access to TorchGeo features like transforms, sampling, and built-in plotting.

```{code-cell} python
single_file = hf_hub_download(repo_id, embedding_files[0], repo_type="dataset")
ds = geoai.load_embedding_dataset("clay", root=single_file)

print(f"Dataset length: {len(ds)}")
```

TorchGeo's `ClayEmbeddings` class expects a date or datetime column in the GeoParquet file, which some community-contributed embedding files may not include. If accessing a sample raises a `KeyError`, that specific file is missing a column TorchGeo expects, and falling back to the plain `geopandas` approach from steps A through D above is the practical solution:

```{code-cell} python
try:
    sample = ds[0]
    print(f"Sample keys: {list(sample.keys())}")
except KeyError as e:
    print(f"Missing column: {e.args[0]}. Falling back to geopandas loading instead.")
```
:::

### Further reading

- OpenGeoAI, ["TorchGeo Embeddings"](https://opengeoai.org/examples/torchgeo_embeddings/) — a worked example of the TorchGeo-based loading path covered in the optional section above.
