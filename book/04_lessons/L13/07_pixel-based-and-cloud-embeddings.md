---
site:
  outline_maxdepth: 2
---

# Pixel-based embeddings

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Pixel-level embeddings and cloud-hosted embedding services
</div>
<!-- markdownlint-enable MD033 -->

---

Every workflow so far has used Clay's patch-based embeddings. This page covers two more systems: TESSERA, pixel-based temporal embeddings you download and work with locally, and AlphaEarth, embeddings you can query and analyze entirely in the cloud without downloading anything.

---

## 1. Motivation

Patch-based embeddings are not the only option, and knowing what pixel-based and cloud-native alternatives offer rounds out your toolkit for choosing the right embedding system for a specific project, exactly the registry-driven decision from earlier in this lesson, now with two more concrete options in view.

---

## 2. Core idea

TESSERA provides 128-channel pixel-based embeddings at 10-meter resolution, compressing a full year of spectral-temporal signal per pixel, downloadable by bounding box, point, or region. AlphaEarth provides annual pixel embeddings accessible directly through {term}`Google Earth Engine (GEE) <Google Earth Engine>`, letting you compute and visualize results in the cloud without downloading any raster data locally at all.

---

## 3. Workflow

### A. Check TESSERA availability

```{code-cell} python
years = geoai.tessera_available_years()

bbox = (0.05, 52.15, 0.25, 52.25)  # Cambridge, UK
count = geoai.tessera_tile_count(bbox=bbox, year=2024)
print(f"{count} tiles available for the specified region")

cambridge_files = geoai.tessera_download(
    bbox=bbox, year=2024, output_dir="./tessera_cambridge", output_format="tiff"
)
```

Checking tile count before downloading, as in the second call, is worth doing for any region larger than a small test area; TESSERA can also be downloaded by a single point (`lon`/`lat`) or from a region file (GeoJSON or Shapefile) instead of a bounding box, and specific bands can be requested with `bands=[...]` to reduce file size when you do not need the full 128 channels.

### B. Visualize embedding bands

```{code-cell} python
geoai.tessera_visualize_rgb(
    str(cambridge_files[0]), bands=(0, 1, 2), title="Cambridge - TESSERA Bands 0, 1, 2"
)
```

Since TESSERA's 128 bands are embedding dimensions rather than physical wavelengths, choosing three of them as red, green, and blue is a visualization convenience, not a true-color image; areas with similar colors share similar temporal dynamics as encoded by the model. Trying different band combinations, for example `bands=(30, 60, 90)`, can surface different aspects of the underlying seasonal signal, worth experimenting with the same way you tried different band combinations for false-color composites in earlier lessons.

### C. Sample embeddings at points

```{code-cell} python
from shapely.geometry import Point

points = gpd.GeoDataFrame(
    {"name": ["Point A", "Point B", "Point C"]},
    geometry=[Point(0.12, 52.20), Point(0.15, 52.18), Point(0.20, 52.22)],
    crs="EPSG:4326",
)

result = geoai.tessera_sample_points(points, year=2024)
print(f"Result shape: {result.shape}")
```

`tessera_sample_points()` appends 128 new columns (`tessera_0` through `tessera_127`) to your points GeoDataFrame, one per embedding dimension, turning point locations directly into feature vectors ready for the same kind of classification workflow from the previous page.

### D. Explore AlphaEarth in Earth Engine

```{code-cell} python
import ee

ee.Authenticate()
ee.Initialize(project="your-ee-project")

dataset = ee.ImageCollection("GOOGLE/SATELLITE_EMBEDDING/V1/ANNUAL")
point = ee.Geometry.Point(-121.8036, 39.0372)

image_2017 = dataset.filterDate("2017-01-01", "2018-01-01").filterBounds(point).first()
image_2024 = dataset.filterDate("2024-01-01", "2025-01-01").filterBounds(point).first()
```

This requires a free Google Earth Engine account and project, separate from the Hugging Face access used earlier in this lesson. `geoai.Map()` includes a built-in AlphaEarth GUI widget (`add_alphaearth_gui()`) for interactive exploration without writing this filtering code directly, useful for a first look at a region before committing to a specific analysis.

### E. Change detection via similarity

```{code-cell} python
dot_prod = image_2017.multiply(image_2024).reduce(ee.Reducer.sum())
```

For unit-normalized embeddings (as AlphaEarth's are), the dot product between two vectors equals their {term}`cosine similarity <Cosine Similarity>`, the same measure used for similarity search earlier in this lesson, here computed per pixel between two years, entirely in the cloud. High similarity (close to 1) indicates a stable pixel; low similarity indicates likely change, without downloading either year's raster data locally.

```{tip}
The dot-product shortcut for cosine similarity only holds because AlphaEarth's vectors are unit-normalized. If you ever compute similarity on embeddings you are not certain are normalized, use an explicit cosine similarity calculation instead of a plain dot product.
```

---

## 4. Python reactivation

`ee.ImageCollection(...).filterDate(...).filterBounds(...).first()` chains several filtering steps together before extracting a single image, the same method-chaining style you may recall from `pandas` or `geopandas` operations, just applied here to Earth Engine's server-side objects. Constructing `points` as a `GeoDataFrame` directly from a dictionary and a `geometry` list, rather than reading from a file, is the same in-memory construction pattern used for small, manually specified datasets throughout this course.

---

## 5. Common pitfalls

- **Forgetting Google Earth Engine needs its own separate account and project.** It is unrelated to the Hugging Face access used for Clay embeddings earlier in this lesson; set it up ahead of time if you plan to use this section.
- **Assuming a bounding box always maps to exactly one TESSERA tile.** Check `tessera_tile_count()` first; larger regions can span multiple tiles.
- **Misreading TESSERA's false-color bands as true spectral colors.** They are embedding dimensions, not wavelengths; color similarity reflects temporal-pattern similarity, not literal surface color.
- **Using a plain dot product for similarity on embeddings that are not unit-normalized.** The dot-product shortcut only works because AlphaEarth's vectors are normalized; other datasets may need an explicit cosine similarity calculation instead.

---

## 6. Mini task

You want to check whether a specific 5-kilometer-wide region experienced significant land-cover change between 2018 and 2023. Would TESSERA or AlphaEarth let you check this without downloading any raster data locally, and why?

:::{dropdown} Sample solution
:class: note

AlphaEarth, accessed through Google Earth Engine, lets you check this without any local download: the dot-product similarity computation runs entirely on Google's servers, and only the final similarity result (or a visualization of it) needs to reach your machine. TESSERA, by contrast, is designed around downloading embeddings (as GeoTIFF or NumPy files) to your own environment for analysis, so checking the same question with TESSERA would require downloading both years' data locally first.
:::

---

## 7. Key takeaways

- TESSERA provides pixel-based, 128-channel temporal embeddings at 10-meter resolution, downloadable by bounding box, point, or region file.
- False-color visualization of TESSERA bands reflects temporal-pattern similarity, not true spectral color.
- AlphaEarth embeddings are queried and analyzed directly in Google Earth Engine, requiring a separate account but no local data download.
- The dot product between unit-normalized embeddings equals cosine similarity, the basis for AlphaEarth's cloud-based change detection.

### Further reading

- OpenGeoAI, ["TESSERA"](https://opengeoai.org/examples/tessera/) — the full, runnable notebook this workflow is based on.
- OpenGeoAI, ["Google Satellite Embedding"](https://opengeoai.org/examples/google_satellite_embedding/) — a related worked example using AlphaEarth in `geoai`.
- Google, ["Satellite Embedding: Similarity Search"](https://developers.google.com/earth-engine/tutorials/community/satellite-embedding-05-similarity-search) — one part of Google's tutorial series on the AlphaEarth dataset, which also covers unsupervised and supervised classification and regression in Earth Engine.
