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
| {abbr}`WRI (World Resources Institute)` / Global Nature Watch data platforms | Environmental monitoring and forest-related context data.                   |
| Project-specific datasets                | Field data, labelled samples, provided training data or institutional data. |

```{admonition} Completeness beats detail
:class: tip
For context layers, completeness can matter more than visual detail. A clean but incomplete layer can lead to misleading summaries.
```

---

### Step 3: Access Overture Maps data

The GeoAI source material shows Overture Maps as a source for building footprints and other vector data. Overture data can be useful for buildings, places, transportation and other contextual features.

```{admonition} Scout Overture data visually first
:class: tip
Before downloading anything, explore the area interactively in the [Overture Maps Explorer](https://explore.overturemaps.org/?feature=transportation.connector.6bf1c035-1390-43e3-ae30-b656e83465c3#4.99/46.8/11.8). Panning and zooming there lets you check feature density and coverage for your study area.
```

The `geoai` package offers two ways to access Overture data. Both rely on the `overturemaps` package, which is installed automatically as a dependency of `geoai` in the SDS320 environment. Both can take a few minutes to run depending on the size of your bounding box and your network speed.

#### Option A: Download to a file

`download_overture_buildings()` uses the Overture Maps CLI to download building footprints for a bounding box and save them to disk:

```python
import os
import geoai

bbox = (8.53, 47.37, 8.54, 47.38)  # example WGS84 bbox near Zurich

output_path = "buildings.geojson"

geoai.download_overture_buildings(
    bbox=bbox,
    output=output_path,
    overture_type="building",
)

full_path = os.path.abspath(output_path)
file_size_mb = os.path.getsize(full_path) / (1024 * 1024)

print(f"Buildings saved to {full_path}")
print(f"File size: {file_size_mb:.2f} MB")
```

The saved file size depends on the bounding box size and building density: a small test area like the one above is typically well under a megabyte, while a city-scale query can reach tens or hundreds of megabytes. Check the file size before scaling up your bounding box.

#### Option B: Load directly as a GeoDataFrame

For more flexibility, `get_overture_data()` returns the data directly as a {term}`GeoDataFrame`, which is convenient for immediate inspection and filtering. It also supports other feature types beyond buildings, such as places, roads, land cover and water:

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

---

### Step 4: Access {abbr}`OSM (OpenStreetMap)`-style data

While Overture Maps is increasingly popular for building footprints, OpenStreetMap (OSM) remains a valuable source for roads, points of interest, land-use polygons and many other features maintained by a global community of volunteers. In GeoAI projects, OSM data is commonly used to generate training labels for tasks such as road extraction, building detection and land-use classification.

```{admonition} Scout OSM coverage before you query
:class: tip
Before querying, explore the area interactively in the [OpenStreetBrowser](https://www.openstreetbrowser.org/#map=18/47.38467/8.54889&basemap=osm-mapnik). It lets you browse OSM features by category and check what is mapped in your study area.
```

The `quackosm` library uses DuckDB to extract OSM data efficiently from {abbr}`PBF (Protocolbuffer Binary Format)` files, and `leafmap.osm` provides convenient wrapper functions around it. You can query by bounding box, by place name or by a custom geometry.

To query by bounding box, use `quackosm_gdf_from_bbox()` with an OSM tag filter:

```python
import leafmap.osm as osm

bbox = (8.53, 47.37, 8.54, 47.38)  # example WGS84 bbox near Zurich

buildings_osm = osm.quackosm_gdf_from_bbox(bbox, tags={"building": True})

print(f"Downloaded {len(buildings_osm)} buildings")
buildings_osm.head()
```

To query by place name, use `quackosm_gdf_from_place()`:

```python
roads = osm.quackosm_gdf_from_place("Basel, Switzerland", tags={"highway": True})

print(f"Downloaded {len(roads)} road segments")
roads.head()
```

For more precise control over the query area, pass a Shapely geometry to `quackosm_gdf_from_geometry()`:

```python
from shapely.geometry import Polygon

polygon = Polygon([
    (8.53, 47.37),
    (8.54, 47.37),
    (8.54, 47.38),
    (8.53, 47.38),
])

natural = osm.quackosm_gdf_from_geometry(polygon, tags={"natural": True})

print(f"Downloaded {len(natural)} natural features")
natural.head()
```

The `quackosm` functions accept any valid OSM tag for filtering. Common tags for GeoAI work include `building`, `landuse`, `natural`, `waterway`, `highway` and `amenity`.

```{admonition} First query is the slow one
:class: tip
The first query for a region can take a while because `quackosm` needs to download and cache the regional PBF file. Subsequent queries in the same region reuse the cached file and run much faster. For larger areas, query size and completeness become important, so start with a small bounding box.
```

---

### Step 5: Access Swiss geodata

If your project area is in Switzerland, the federal geoportal [map.geo.admin.ch](https://map.geo.admin.ch) is backed by a set of free public REST APIs from swisstopo. Most endpoints need no API key, which makes them convenient for use, but swisstopo does enforce rate limits on high-frequency requests.

```{admonition} Scout the geoportal before you code
:class: tip
Before writing any code, explore your study area on [map.geo.admin.ch](https://map.geo.admin.ch) itself. Browsing the available layers there helps you identify which dataset or layer ID you actually need before you query it programmatically.
```

The relevant services are:

| Service | Base URL | Purpose |
| --- | --- | --- |
| Core REST API | `api3.geo.admin.ch` | Geocoding, the "identify" endpoint (query features at a point), feature search, height/elevation lookup. |
| STAC catalog | `data.geo.admin.ch` | Downloadable datasets such as orthophotos, elevation models and 3D building data. |
| WMTS tile service | `wmts.geo.admin.ch` | Map tiles and imagery, and the basis for shareable map.geo.admin.ch links. |

```{admonition} Same STAC pattern, new provider
:class: seealso
You already used the `data.geo.admin.ch` STAC catalog on the previous page to search SWISSIMAGE orthophotos with `requests`. The same STAC pattern applies to other Swiss datasets, such as elevation models, listed in that catalog.
```

Because most of these endpoints are plain REST services, you can query them directly with `requests`, without a dedicated Python wrapper. The following example uses the "identify" endpoint with an envelope (bounding box) geometry to download the [Suitability of roofs for the use of solar energy](https://www.bfe.admin.ch/solarenergie-eignung-daecher) dataset, published by the Swiss Federal Office of Energy (SFOE), for a small residential quarter in Zurich, and loads the result directly into a {term}`GeoDataFrame`:

```{code-cell} python
import requests
import geopandas as gpd
from shapely.geometry import shape

identify_url = "https://api3.geo.admin.ch/rest/services/api/MapServer/identify"

bbox = "8.5470,47.3950,8.5490,47.3970"  # small quarter in Zurich, WGS84

response = requests.get(
    identify_url,
    params={
        "geometry": bbox,
        "geometryType": "esriGeometryEnvelope",
        "geometryFormat": "geojson",
        "sr": 4326,
        "layers": "all:ch.bfe.solarenergie-eignung-daecher",
        "tolerance": 0,
        "mapExtent": "8.50,47.35,8.60,47.45",
        "imageDisplay": "800,600,96",
        "returnGeometry": "true",
    },
)
response.raise_for_status()

results = response.json()["results"]

roofs = gpd.GeoDataFrame(
    [r["properties"] for r in results],
    geometry=[shape(r["geometry"]) for r in results],
    crs="EPSG:4326",
)

print(f"Downloaded {len(roofs)} roof facets")
roofs[["klasse_text", "flaeche", "stromertrag"]].head()
```

Each roof facet carries a suitability class (`klasse_text`, ranging from low to top suitability), its area in square metres (`flaeche`) and an estimated annual electricity yield in kWh (`stromertrag`). This dataset is only offered as a single whole-of-Switzerland download through the STAC catalog, so for a small project area, querying by bounding box like this is more practical than downloading the full national file.

You can also query features at a specific point using the "identify" endpoint. This is useful for contextual lookups, for example checking whether a location sits on a mapped hiking trail:

```python
identify_url = "https://api3.geo.admin.ch/rest/services/api/MapServer/identify"

response = requests.get(
    identify_url,
    params={
        "geometry": "8.5480,47.3980",
        "geometryType": "esriGeometryPoint",
        "sr": 4326,
        "layers": "all:ch.swisstopo.swisstlm3d-wanderwege",
        "tolerance": 200,
        "mapExtent": "8.50,47.35,8.60,47.45",
        "imageDisplay": "800,600,96",
    },
)
response.raise_for_status()

results = response.json()["results"]
print(f"Found {len(results)} nearby trail segments")
if results:
    print(results[0]["attributes"])
```

A third pattern is feature search: querying a layer's attributes by name instead of by location. The following example searches the swisstopo district-boundary layer for districts named "Bern":

```python
find_url = "https://api3.geo.admin.ch/rest/services/api/MapServer/find"

response = requests.get(
    find_url,
    params={
        "layer": "ch.swisstopo.swissboundaries3d-bezirk-flaeche.fill",
        "searchText": "Bern",
        "searchField": "name",
        "returnGeometry": "false",
    },
)
response.raise_for_status()

for feature in response.json()["results"]:
    print(feature["attributes"]["name"], feature["attributes"]["flaeche"], "ha")
```

```{admonition} Districts are not neighbourhoods
:class: caution
This layer covers administrative districts (Bezirke), which sit between canton and municipality and are coarser than a city's internal quarters or neighbourhoods. Intra-city boundaries such as Geneva's or Bern's own quartiers are usually published by the city's open data portal rather than by swisstopo — always check which administrative level a boundary dataset actually represents.
```

```{admonition} APIs evolve — verify before you build
:class: attention
Endpoint parameters and available layer IDs can change over time. Check the [api3.geo.admin.ch service documentation](https://api3.geo.admin.ch/services/sdiservices.html) for the current parameter list before building a query for your project.
```

Project check: direct API access is convenient for small, targeted lookups, such as a single quarter or a handful of points. For bulk downloads over a larger area, prefer the STAC catalog or an official bulk-download tool instead of looping over the REST API.

---

### Step 6: Access global datasets

Alongside the specific sources above, a growing number of platforms curate global environmental and urban monitoring datasets, each with its own interactive map viewer. These are worth browsing manually first, since not every platform offers a convenient API, and a manual download through the browser is sometimes the most practical route into a project.

```{admonition} Browse first, then decide
:class: tip
Interactive viewers let you check coverage, resolution and recency for your exact study area before you invest time into a data pipeline. Treat browsing as part of Step 2 (choosing a source), not as a detour from it.
```

| Platform | What it offers | Explore |
| --- | --- | --- |
| World Resources Institute (WRI) Data Explorer | A long-established, broad catalogue of curated environmental and socioeconomic datasets covering forests, water, cities, climate and land use. | [wri.org/data](https://www.wri.org/index%2ephp/data) |
| Global Nature Watch | A newer WRI platform for near-real-time, satellite-based monitoring of forest and land-cover change worldwide. | [globalnaturewatch.org/map](https://globalnaturewatch.org/map/) |
| Fields of the World (FTW) | A benchmark and interactive inference app for machine-learning-derived agricultural field boundaries, useful as training data or comparison in cropland-mapping projects. | [fieldsofthe.world](https://fieldsofthe.world/ftw-inference-app/) |
| Global Building Atlas (GBA) | AI-generated global building footprints and height data, viewable through a Google Earth Engine app. | [sat-io.earthengine.app/view/gba](https://sat-io.earthengine.app/view/gba) |

A few more platforms worth knowing about:

* **ESA WorldCover**, a global 10 m land-cover map with its own interactive viewer: [esa-worldcover.org](https://esa-worldcover.org)
* The **Awesome GEE Community Datasets** catalogue, the same `sat-io` project behind the Global Building Atlas, which hosts dozens of similar community-built Earth Engine apps for global monitoring themes: [samapriya.github.io/awesome-gee-community-datasets](https://samapriya.github.io/awesome-gee-community-datasets/)

```{admonition} There is more out there
:class: note
Environmental and urban monitoring is a fast-moving field, and new global datasets and viewers appear frequently, especially ones derived from machine learning on satellite imagery. It is worth a short search for platforms specific to your project's theme, such as water, biodiversity or mobility, before settling on a data source.
```

---

### Step 7: Check vector data quality

After loading vector data, inspect it before using it.

```{code-cell} python
print("Rows:", len(buildings))
print("CRS:", buildings.crs)
print("Geometry types:", buildings.geom_type.value_counts())
print("Bounds:", buildings.total_bounds)
```

Also map a small sample with your imagery or basemap. Check whether the data align spatially and whether obvious features are missing.

---

### Step 8: Save vector data

For project work, prefer robust and reproducible formats.

Common choices include:

| Format     | Use                                                              |
| ---------- | ---------------------------------------------------------------- |
| GeoPackage | Good general-purpose local vector format.                        |
| GeoParquet | Efficient for larger vector datasets and modern cloud workflows. |
| GeoJSON    | Easy to inspect and share for small datasets.                    |
| Shapefile  | Still common, but has quite some practical limitations.          |

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
