---
site:
  outline_maxdepth: 2
---

# Finding suitable data

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning a project idea into concrete data search criteria
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why data search matters

Before you download anything, you need to know what kind of data would actually support your project.

A strong SDS320 project does not start with a random dataset. It starts with a question, then searches for data that can answer it. This page helps you translate your project idea into search criteria and a first data inventory.

In the next page, you will apply these ideas to remote sensing data and {abbr}`STAC (SpatioTemporal Asset Catalog)` search workflows.

---

## 2. Core idea

Data search is a filtering process.

You begin with many possible datasets and gradually reduce them using project criteria:

```text
Research question
→ study area
→ time period
→ data type
→ resolution
→ quality
→ licence
→ access route
→ file size
→ project fit
```

The output of this page is not a finished dataset. It is a reasoned shortlist of candidate data sources.

---

## 3. Workflow

### Step 1: Start from the project question

Write down what your data must show.

Examples:

```text
I need imagery where individual buildings are visible.
```

```text
I need repeated observations before and after a flood event.
```

```text
I need land-cover information for a defined city region.
```

This step matters because different questions need different data. Object-level questions may need high-resolution imagery. Long-term change questions may need a long time series. Context questions may need vector data such as roads, buildings, administrative areas or land use.

---

### Step 2: Define area and time

Most spatial data portals allow filtering by space and time.

Define:

```text
Study area:
Time period:
Season or date constraints:
Minimum useful spatial resolution:
```

For many web services, the first spatial filter is a {term}`Bounding Box`.

Example:

```text
bbox = [min_lon, min_lat, max_lon, max_lat]
```

```{warning}
Many STAC searches expect bounding boxes in WGS84 longitude/latitude coordinates, even when the downloaded data are stored in a projected CRS.
```

---

### Step 3: Decide what data type you need

Most SDS320 projects use a combination of {term}`Raster Data` and vector data.

| Data need                       | Typical data type       |
| ------------------------------- | ----------------------- |
| Satellite or aerial imagery     | Raster                  |
| Elevation, temperature, indices | Raster                  |
| Buildings, roads, boundaries    | Vector                  |
| Training labels or masks        | Vector or raster        |
| Validation samples              | Vector, raster or table |
| Context information             | Vector or raster        |

The type of data affects your workflow, file structure and preprocessing needs.

---

### Step 4: Check suitability

Before downloading large files, inspect the metadata or preview where possible.

Check:

* spatial coverage,
* temporal coverage,
* spatial resolution,
* spectral bands,
* cloud cover or other quality indicators,
* coordinate reference system,
* file format,
* licence,
* access method,
* expected file size,
* documentation quality.

A dataset can be open and still not suitable for your project.

---

### Step 5: Create a data inventory

A data inventory is a table that records candidate datasets and why they may or may not fit.

Use this structure:

```text
Dataset:
Provider:
Data type:
Study area fit:
Time period fit:
Resolution:
Format:
Access route:
Licence:
Main use in project:
Main limitation:
Decision:
```

You can keep this in a Markdown file, spreadsheet or notebook table. The important part is that the reasoning is visible.

---

## 4. Python reactivation

A small project data inventory can be represented as a list of dictionaries and converted to a table.

```{code-cell} python
import pandas as pd

data_sources = [
    {
        "dataset": "Sentinel-2 Level-2A",
        "provider": "Planetary Computer / ESA",
        "data_type": "raster",
        "main_use": "multispectral imagery",
        "main_limitation": "cloud cover and 10 m resolution",
        "decision": "candidate",
    },
    {
        "dataset": "Overture Maps buildings",
        "provider": "Overture Maps",
        "data_type": "vector",
        "main_use": "building context or labels",
        "main_limitation": "quality varies by area",
        "decision": "check locally",
    },
]

inventory = pd.DataFrame(data_sources)
inventory
```

This is not yet data acquisition code. It is project thinking in a structured format.

---

## 5. Common pitfalls

| Pitfall                                 | How to avoid it                                                        |
| --------------------------------------- | ---------------------------------------------------------------------- |
| Starting with a dataset but no question | Write what the data should help you answer.                            |
| Downloading too much too early          | Test one small area or item first.                                     |
| Ignoring licence information            | Record reuse conditions before using the data.                         |
| Confusing availability with suitability | Check resolution, time period, quality and format.                     |
| Forgetting vector context data          | Consider buildings, roads, boundaries or land-use data where relevant. |
| Not recording failed searches           | Document why a dataset was rejected.                                   |

---

## 6. Mini task

Create a first data inventory for your project.

Include at least:

* one raster candidate,
* one vector or contextual candidate,
* one limitation for each dataset,
* one decision about what to test next.

:::{note} Sample solution
:class: dropdown

```text
Dataset: Sentinel-2 Level-2A
Provider: Microsoft Planetary Computer / ESA
Data type: Raster
Study area fit: Covers the study area
Time period fit: Available for recent years
Resolution: 10 m for visible and NIR bands
Format: Cloud Optimized GeoTIFF assets via STAC
Access route: STAC search through Planetary Computer
Licence: Check collection metadata before use
Main use in project: land-cover or vegetation analysis
Main limitation: clouds and moderate spatial resolution
Decision: test one low-cloud scene

Dataset: Overture Maps buildings
Provider: Overture Maps
Data type: Vector
Study area fit: likely available, needs local check
Time period fit: current map data, not a historical time series
Resolution: vector building footprints
Format: GeoParquet or GeoJSON depending on access method
Access route: Overture tools or GeoAI helper functions
Licence: Check provider documentation before publication
Main use in project: building context or validation support
Main limitation: completeness may vary by place
Decision: download a small test area
```

:::

---

## 7. Key takeaways

* Data search starts from the project question.
* Suitable data must fit area, time, resolution, quality, licence and method.
* A data inventory makes project decisions visible.
* Test small before downloading large datasets.
* Record rejected datasets as part of your project reasoning.
