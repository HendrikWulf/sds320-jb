---
site:
  outline_maxdepth: 2
---

# Citing data

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Recording where your data came from and what others need to know about it
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

Data acquisition is unfortunately not complete when a file appears in your folder. You also should know where the data came from, whether you are allowed to use it, how it was accessed, what limitations it has and how another person could retrieve or understand it.

For SDS320, this matters because your final project includes a public repository and a short report. Another student should be able to understand your data choices and reproduce the main workflow.

In the previous pages, you identified raster and vector candidates. Here, you turn those choices into documentation.

---

## 2. Core idea

Every dataset in your project should have a short documentation trail:

```text
source
→ access route
→ licence
→ version or date
→ spatial and temporal coverage
→ processing status
→ limitations
→ citation or attribution
```

This documentation protects your project from common problems: forgotten download links, unclear licences, missing metadata, inconsistent filenames and results that cannot be reproduced.

---

## 3. Workflow

### Step 1: Record source information

For every dataset, record:

```text
Dataset name:
Provider:
Access URL or API:
Download or access date:
Version or collection ID:
Licence:
Citation or attribution text:
```

Best do this before preprocessing. Once data are renamed, clipped or merged, it becomes harder to reconstruct their origin.

---

### Step 2: Record metadata

For spatial data, document:

```text
Study area:
Original spatial extent:
Coordinate reference system:
Spatial resolution or geometry type:
Temporal coverage:
Acquisition date or period:
File format:
```

This information helps you explain whether the data fit your research question.

---

### Step 3: Separate raw and processed

Keep raw downloads separate from processed files.

A simple structure is:

```text
data/
├── raw/
├── processed/
└── training/
```

Raw data should remain unchanged. Processed data can include clipped, reprojected, cleaned, merged or tiled files.

This makes it easier to rerun processing and explain what changed.

---

### Step 4: Document access steps

For large data, restricted data or cloud-hosted assets, do not assume the reader can see what you did.

Write short instructions:

```text
To obtain the Sentinel-2 data:
1. Open the Planetary Computer STAC API.
2. Search collection `sentinel-2-l2a`.
3. Use the bbox and date range listed below.
4. Filter cloud cover below the chosen threshold.
5. Download or load the selected assets.
```

If the exact data cannot be shared publicly, explain how the data can be accessed or why they cannot be redistributed.

---

### Step 5: Track limitations

Data limitations belong in your notes, report and sometimes figure captions.

Common limitations include:

* clouds or shadows,
* missing data,
* coarse spatial resolution,
* changing acquisition dates,
* incomplete vector coverage,
* uncertain labels,
* licence restrictions,
* mismatch between data date and project question,
* manual processing steps.

A clear limitation is better than a hidden weakness.

---

## 4. Python reactivation

You can keep a lightweight data log in a table.

```{code-cell} python
import pandas as pd

data_log = pd.DataFrame(
    [
        {
            "dataset": "Sentinel-2 Level-2A",
            "provider": "Microsoft Planetary Computer / ESA",
            "collection": "sentinel-2-l2a",
            "access_date": "YYYY-MM-DD",
            "role": "input imagery",
            "licence_note": "check collection metadata",
            "limitation": "cloud cover and 10 m resolution",
        }
    ]
)

data_log
```

You can export this table to your repository:

```{code-cell} python
from pathlib import Path

output_path = Path("data/data_inventory.csv")
output_path.parent.mkdir(parents=True, exist_ok=True)

data_log.to_csv(output_path, index=False)
```

---

## 5. Data citation in the report

Your short report should not contain a full data-management manual. It should include enough information for the reader to understand the data basis.

A compact sentence can work well:

```text
The analysis used Sentinel-2 Level-2A imagery accessed through the Microsoft Planetary Computer STAC API for [area] during [date range]. Scenes were filtered by [quality criterion], and the main limitation was [limitation].
```

For vector data:

```text
Building footprint data were obtained from [provider] for [area] and used as [context/training/validation]. Completeness and alignment were checked visually against [imagery/source].
```

---

## 6. Common pitfalls

| Pitfall                                       | How to avoid it                                                       |
| --------------------------------------------- | --------------------------------------------------------------------- |
| Downloading data without recording the source | Fill in a data log immediately.                                       |
| Forgetting licence information                | Check provider metadata before using data in figures or repositories. |
| Mixing raw and processed files                | Keep separate folders.                                                |
| Renaming files without preserving meaning     | Use names that include source, date, area and processing level.       |
| Citing only the platform, not the dataset     | Record dataset or collection name as well as access route.            |
| Hiding data limitations                       | State limitations briefly and clearly.                                |

---

## 7. Mini task

Create one data documentation entry for a dataset you may use.

Use this template:

```text
Dataset name:
Provider:
Access route:
Access date:
Version / collection / release:
Spatial coverage:
Temporal coverage:
Resolution or geometry type:
Format:
Licence:
Role in project:
Processing planned:
Main limitation:
How I will cite or acknowledge it:
```

:::{note} Sample solution
:class: dropdown

```text
Dataset name: Sentinel-2 Level-2A
Provider: ESA, accessed through Microsoft Planetary Computer
Access route: Planetary Computer STAC API
Access date: YYYY-MM-DD
Version / collection / release: sentinel-2-l2a
Spatial coverage: project AOI
Temporal coverage: selected summer date range
Resolution or geometry type: 10 m visible and NIR bands, other bands at coarser resolution
Format: Cloud Optimized GeoTIFF assets
Licence: check collection metadata before final publication
Role in project: input imagery for land-cover analysis
Processing planned: filter by cloud cover, download selected bands, clip to AOI
Main limitation: cloud cover and spatial resolution
How I will cite or acknowledge it: include provider, collection and access route in report and README
```

:::

---

## 8. Key takeaways

* Data acquisition includes documentation, not only downloading.
* Record source, access route, licence, coverage, format and limitations early.
* Separate raw and processed data.
* Keep a data inventory in the repository.
* Good data documentation supports reproducibility, reporting and presentation.
