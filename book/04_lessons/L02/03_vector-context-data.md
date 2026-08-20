---
site:
  outline_maxdepth: 2
---

# Vector and contextual data

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Using buildings, roads, places and boundaries to support spatial analytics projects
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this page matters

Remote sensing imagery often needs context. A satellite image may show pixels, but your project may need buildings, roads, land-use polygons, administrative boundaries, reference samples or validation data to make the analysis meaningful.

In the previous page, you searched for raster imagery. This page focuses on vector and contextual data that can support the same project.

The next page explains how to document and cite both raster and vector sources.

---

## 2. Core idea

Vector data can support an SDS320 project in several roles:

| Role | Example |
| --- | --- |
| Study area definition | boundary polygon, city extent, protected area |
| Context | roads, rivers, land use, buildings |
| Training data | building footprints, road lines, labelled polygons |
| Validation data | independent reference points or polygons |
| Interpretation | administrative units, population context, infrastructure |
| Visualisation | map overlays, labels, location context |

The same vector dataset can be helpful for one project and misleading for another. You need to check completeness, date, geometry quality and licence.

---

## 3. Workflow

### Step 1: Identify the role of vector data

Consider what you need the vector data for before downloading it.

Examples:

```text
I need building footprints as context for interpreting model outputs.
```

```text
I need administrative boundaries to summarise predicted change by district.
```

```text
I need road data to compare detected linear features with mapped roads.
```

This avoids collecting vector layers that do not support the analysis.

---

### Step 2: Choose a vector source

Possible sources include:

| Source                                   | Possible use                                                                |
| ---------------------------------------- | --------------------------------------------------------------------------- |
| Overture Maps                            | Buildings, places, transportation, divisions and base features.             |
| OpenStreetMap                            | Roads, buildings, amenities, land use and other volunteered map features.   |
| National or regional open data portals   | Official boundaries, cadastral layers, land-use data or infrastructure.     |
| WRI / Global Nature Watch data platforms | Environmental monitoring and forest-related context data.                   |
| Project-specific datasets                | Field data, labelled samples, provided training data or institutional data. |

```{tip}
For context layers, completeness can matter more than visual detail. A clean but incomplete layer can lead to misleading summaries.
```

---

### Step 3: Access Overture Maps data

The GeoAI source material shows Overture Maps as a source for building footprints and other vector data. Overture data can be useful for buildings, places, transportation and other contextual features.

A helper function from the `geoai` package can return Overture data as a {term}`GeoDataFrame`:

```{code-cell} python
import geoai

bbox = (8.53, 47.37, 8.54, 47.38)  # example WGS84 bbox near Zurich

buildings = geoai.get_overture_data(
    overture_type="building",
    bbox=bbox,
    output="buildings.parquet",
)

print(f"Downloaded {len(buildings)} buildings")
buildings.head()
```

<!-- TODO: confirm whether `geoai.get_overture_data()` and the required `overturemaps` dependency are included in the official SDS320 environment. -->

If the helper function is not available, use the official Overture documentation and tools instead.

---

### Step 4: Access {abbr}`OSM (OpenStreetMap)`-style data

OpenStreetMap data can be useful for roads, buildings, points of interest and land-use context. The GeoAI source material mentions libraries such as `osmnx`, `ohsome`, `quackosm` and `leafmap.osm`.

An example pattern is:

```python
import leafmap.osm as osm

bbox = (8.53, 47.37, 8.54, 47.38)

roads = osm.quackosm_gdf_from_bbox(
    bbox,
    tags={"highway": True},
)

roads.head()
```

This is useful for small tests. For larger areas, query size and completeness become important.

<!-- TODO: confirm which OpenStreetMap access library is recommended for SDS320. -->

---

### Step 5: Check vector data quality

After loading vector data, inspect it before using it.

```{code-cell} python
print("Rows:", len(buildings))
print("CRS:", buildings.crs)
print("Geometry types:", buildings.geom_type.value_counts())
print("Bounds:", buildings.total_bounds)
```

Also map a small sample with your imagery or basemap. Check whether the data align spatially and whether obvious features are missing.

---

### Step 6: Save vector data in a useful format

For project work, prefer robust and reproducible formats.

Common choices include:

| Format     | Use                                                              |
| ---------- | ---------------------------------------------------------------- |
| GeoPackage | Good general-purpose local vector format.                        |
| GeoParquet | Efficient for larger vector datasets and modern cloud workflows. |
| GeoJSON    | Easy to inspect and share for small datasets.                    |
| Shapefile  | Still common, but has practical limitations.                     |

Record which format you used and why.

---

## 4. Python reactivation

Vector data workflows often use familiar GeoPandas operations:

```{code-cell} python
# Inspect columns
buildings.columns

# Select useful columns
selected = buildings[["geometry"]].copy()

# Check approximate feature count
len(selected)
```

If you calculate areas, make sure the data are in a projected CRS with metre-based units.

```python
# Example only: use a suitable CRS for your own study area
buildings_projected = buildings.to_crs("EPSG:2056")
buildings_projected["area_m2"] = buildings_projected.area
```

---

## 5. Common pitfalls

| Pitfall                                                      | How to avoid it                                            |
| ------------------------------------------------------------ | ---------------------------------------------------------- |
| Treating vector data as complete truth                       | Check completeness and source limitations.                 |
| Ignoring CRS                                                 | Confirm that vector and raster data align before analysis. |
| Using huge vector datasets too early                         | Start with a small bounding box.                           |
| Using building footprints as labels without checking quality | Inspect alignment with imagery.                            |
| Forgetting licence and attribution                           | Record provider and reuse conditions.                      |
| Saving only processed data                                   | Preserve notes about original source and access route.     |

---

## 6. Mini task

Find one vector or contextual dataset for your project.

Write:

```text
Dataset:
Provider:
Feature type:
Role in project:
Access method:
CRS:
Format:
Quality concern:
Licence or attribution note:
Decision:
```

:::{note} Sample solution
:class: dropdown

```text
Dataset: Overture Maps buildings
Provider: Overture Maps
Feature type: building footprints
Role in project: context layer and possible validation support
Access method: bbox query through Overture tools or GeoAI helper function
CRS: check after download
Format: GeoParquet
Quality concern: completeness and alignment may vary by location
Licence or attribution note: check Overture documentation before publication
Decision: test small AOI and overlay on imagery
```

:::

---

## 7. Key takeaways

* Vector data can define study areas, provide context, support labels or help evaluate outputs.
* Choose vector data based on its role in your project.
* Inspect completeness, CRS, geometry type and spatial alignment.
* Start with a small area before downloading large vector datasets.
* Document provider, access method, licence and limitations.
