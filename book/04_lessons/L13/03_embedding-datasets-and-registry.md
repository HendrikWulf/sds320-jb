---
site:
  outline_maxdepth: 2
---

# Embedding datasets

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Finding and loading pre-computed embedding datasets
</div>
<!-- markdownlint-enable MD033 -->

---

You know what an embedding is and the two formats it can take. This page shows you how to survey what is actually available before choosing a dataset, since the ecosystem has grown to include several foundation models with different coverage, resolution, and licensing.

---

## 1. Motivation

Downloading the wrong embedding dataset, one that does not cover your study area, or whose resolution is too coarse for your question, wastes time. `geoai`'s registry lets you check all of this before downloading anything at all.

---

## 2. Core idea

`geoai` provides a unified registry of {term}`embedding <Embedding>` datasets, backed by {term}`TorchGeo` dataset classes, spanning multiple foundation models. You can list everything available, filter by format, and inspect detailed metadata for any specific dataset before deciding what to use.

---

## 3. Workflow

### A. List all available datasets

```{code-cell} python
import geoai

df = geoai.list_embedding_datasets(verbose=False)
df
```

This returns a table with each dataset's name, underlying class, format (`patch` or `pixel`), spatial extent, resolution, temporal coverage, and embedding dimensionality. The registry spans a genuinely wide range: from Clay's 768-dimensional global patch embeddings to TESSERA's 128-dimensional pixel embeddings at 10-meter resolution, and several others in between.

```{tip}
Exact dataset names and year ranges can shift slightly across `geoai` and TorchGeo releases. The overall workflow in this lesson stays the same even if specific numbers in the registry table differ slightly from what is shown here.
```

### B. Filter by format

```{code-cell} python
patch_datasets = geoai.list_embedding_datasets(kind="patch", verbose=False)
pixel_datasets = geoai.list_embedding_datasets(kind="pixel", verbose=False)
```

Filtering by `kind` narrows the table to just patch-based or just pixel-based datasets, useful once you already know, from the previous page's distinction, which format your project actually needs.

### C. Inspect a specific dataset

```{code-cell} python
info = geoai.get_embedding_info("clay")
for key, value in info.items():
    print(f"{key}: {value}")
```

`get_embedding_info()` returns detailed metadata for one dataset: its class name, format, spatial and temporal coverage, dimensionality, data type, license, and links to the original paper and data source. This is the level of detail worth checking before committing to a dataset for a project.

### D. Choosing a dataset for your project

Match the registry's fields directly to your project's needs: spatial extent and resolution against your study area and the scale of the features you care about, temporal extent against the dates you need, and license terms against how you plan to use the results, especially if your project output will be shared or published. A higher embedding dimension is not automatically "better"; it mainly reflects how much information the foundation model packs per vector, which trades off against storage size and processing cost.

---

## 4. Python reactivation

`df` returned by `list_embedding_datasets()` is a standard `pandas` DataFrame, the same object type you have filtered, sorted, and inspected throughout this course. `info.items()` iterates over a dictionary's key-value pairs together, the same pattern used for inspecting metadata dictionaries in several earlier lessons.

---

## 5. Common pitfalls

- **Skipping the license check.** If your project's output will be shared, published, or used commercially, license terms matter and are easy to overlook when you are focused on technical fit alone.
- **Assuming higher dimensionality means a better dataset.** Dimension count reflects how much the model packs into each vector, not an automatic ranking of quality for your specific task.
- **Not checking temporal coverage against your project's actual date range.** A dataset with excellent spatial coverage is still the wrong choice if it does not include the years your project needs.
- **Choosing a dataset before checking its spatial extent covers your specific study area.** "Global" coverage in the registry does not guarantee uniform density or quality everywhere; verify against your specific region where possible.

---

## 6. Mini task

Your project needs 10-meter resolution embeddings covering a specific U.S. county, for a change-detection question spanning 2018 to 2023. Using the registry fields described on this page, which two or three fields would you check first, and why?

:::{dropdown} Sample solution
:class: note

Spatial resolution first, to confirm 10 meters or finer is actually available (ruling out coarser datasets like the 0.25-degree Copernicus embeddings mentioned in the registry). Temporal extent second, to confirm 2018 through 2023 falls within the dataset's covered years, essential for a change-detection question spanning that specific range. Spatial extent third, to confirm the dataset actually includes the specific U.S. county in question, since "Global" coverage does not guarantee the same density or quality everywhere. Dimensionality and license are still worth checking, but these three fields most directly determine whether a candidate dataset can answer the stated question at all.
:::

---

## 7. Key takeaways

- `list_embedding_datasets()` surveys everything available; `kind="patch"` or `kind="pixel"` filters by format.
- `get_embedding_info()` returns detailed metadata, including resolution, temporal coverage, dimensionality, and license, for one specific dataset.
- Match registry fields to your project's actual needs before downloading; a technically impressive dataset is the wrong choice if its coverage, resolution, or license do not fit.
- The next page uses this registry to select and work with Clay Foundation Model embeddings specifically.
