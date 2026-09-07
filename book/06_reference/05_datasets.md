---
site:
  outline_maxdepth: 2
---

# Datasets

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Finding spatial data that fits your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## Why dataset choice matters

The dataset you choose shapes almost everything else about your project: what research question is actually answerable, which methods are feasible, how much preprocessing you will need to do, and how you will be able to evaluate your results. Choosing data before you have a clear question is one of the most common ways a project drifts off course; use the checklist below before committing to a source.

---

## Dataset selection checklist

Before committing to a dataset, check:

- [ ] **Spatial coverage** — does it actually cover your study area?
- [ ] **Temporal coverage** — does it include the dates or period your project needs?
- [ ] **Spatial resolution** — is it fine enough to resolve the features you care about?
- [ ] **Data format** — can you actually read and work with it (GeoTIFF, GeoJSON, GeoParquet, Shapefile, and so on)?
- [ ] **Coordinate reference system** — is it documented, and compatible with your other data?
- [ ] **License** — are you allowed to use it the way your project needs, including sharing results?
- [ ] **Access method** — direct download, an API, a cloud catalog, or something requiring approval?
- [ ] **File size** — realistic to download and process with the time and hardware you have?
- [ ] **Update frequency** — does it matter whether the data is current, or is a fixed historical version fine?
- [ ] **Documentation** — is there enough information to understand what the data actually represents?
- [ ] **Quality and uncertainty** — does the provider document known limitations or accuracy?
- [ ] **Citation requirements** — do you know how to cite it correctly in your report?

---

## Dataset categories

### Remote sensing imagery

- [Microsoft Planetary Computer Data Catalog](https://planetarycomputer.microsoft.com/catalog) — a large, cloud-hosted catalog of satellite and aerial imagery, including Sentinel-2, Landsat, and NAIP, accessible through a STAC API.
- [Google Earth Engine Data Catalog](https://developers.google.com/earth-engine/datasets) — a large catalog of remote sensing and geospatial datasets accessible through Earth Engine.
- [Awesome Satellite Imagery Datasets](https://github.com/chrieke/awesome-satellite-imagery-datasets) — a curated list of satellite imagery datasets for machine learning.

### Vector and contextual data

- [OpenStreetMap](https://www.openstreetmap.org) — community-maintained vector data covering roads, buildings, land use, and points of interest worldwide.
- [Overture Maps](https://overturemaps.org/) — open map data built from multiple sources, including buildings, places, and transportation networks.
- [map.geo.admin.ch](https://map.geo.admin.ch/) — the Swiss federal geoportal, useful for Switzerland-focused projects.

### Environmental data

- [Global Nature Watch](https://globalnaturewatch.org/) — open data on forests, land cover, climate, and biodiversity.
- [Global Surface Water](https://global-surface-water.appspot.com/) — open data on annual surface water.
- [EarthEnv](https://www.earthenv.org/) — open data on biodiversity and climate.
- [Copernicus Land Monitoring Service](https://eu-space.europa.eu/copernicus-land) - European land cover and bio-geophysical variables
  
### Urban and public administration data

- [World Resources Institute Data](https://www.wri.org/data) — open data on cities, land use, water, and related topics.
- [data.geo.admin.ch](https://data.geo.admin.ch/) — Swiss federal open geodata portal.
- [Natural Earth](https://www.naturalearthdata.com/) — free vector and raster map data at multiple scales, useful for administrative boundaries and basemap layers.

### Benchmark and training datasets

- [ImageNet](https://www.image-net.org/about.php) — a large, general-purpose image classification benchmark; the source of ImageNet pre-training used by many encoders in this course.
- [COCO Dataset](https://cocodataset.org/#home) — a widely used benchmark for object detection, segmentation, and captioning.
- [Open Images Dataset](https://storage.googleapis.com/openimages/web/index.html) — a large annotated image dataset for classification, detection, and segmentation.

---

## Dataset table

The table below lists datasets already used in SDS320 lesson material, as a starting point for understanding what kind of data pairs with which method.

| Dataset | Type | Possible use | Coverage | Access | Notes |
| --- | --- | --- | --- | --- | --- |
| NWPU-VHR-10 | Very-high-resolution imagery + object annotations | Object detection | 10 classes, fixed benchmark set | Source Cooperative download | Used in the object detection lesson as a multi-class detection benchmark |
| Fields of the World (FTW) | Sentinel-2 imagery + instance masks | Instance segmentation | 24 countries; course example uses Luxembourg | Source Cooperative download via `geoai` | Field-boundary benchmark with per-field instance IDs already encoded |
| Chesapeake Land Cover | NAIP imagery + land-cover labels | Multi-class semantic segmentation | Chesapeake Bay watershed, 13 classes | Source Cooperative download | Used as the multi-class land-cover example in the segmentation lesson |
| EuroSAT | Sentinel-2 image patches + scene labels | Image classification | Europe, 10 land-use/land-cover classes | Public download | Used in the image recognition lesson |
| Earth Surface Water Dataset | Sentinel-2 imagery + water masks | Semantic segmentation | Multiple scenes; six spectral bands | Source Cooperative download | Used in the multispectral water-mapping example |
| Clay Foundation Model embeddings | Patch-based embeddings (GeoParquet) | Similarity search, clustering, lightweight classification | Example uses San Francisco Bay Area | Hugging Face Hub | Introduced in the satellite embeddings lesson |
| TESSERA embeddings | Pixel-based embeddings (128 channels, 10 m) | Fine-grained classification, temporal analysis | Downloadable by bounding box, point, or region | `geoai` download functions | Introduced alongside Clay in the embeddings lesson |
| AlphaEarth (Google Satellite Embedding) | Annual pixel embeddings | Similarity search, change comparison | Global, 2017 onward | Google Earth Engine | Cloud-based; no local download required |

---

## Documenting datasets in your project

For every dataset your project uses, record:

- dataset name,
- provider,
- URL or access path,
- date accessed,
- license,
- processing steps you applied,
- known limitations.

```{tip}
Keep this documentation in your repository, not only in your head. A short `data/README.md` describing exactly this information for each dataset saves you from having to reconstruct it later, and is exactly the kind of documentation a reproducible project needs.
```

---

## Common pitfalls

- **Choosing data before defining the question.** This tends to force the question to fit whatever data happened to be convenient, rather than the other way around.
- **Ignoring licenses.** Some datasets restrict commercial use, redistribution, or require specific attribution; check before you build a project around one.
- **Using data that is too large for your time and hardware.** A dataset that takes days to download or process can consume most of your project timeline before any analysis happens.
- **Combining datasets with different CRS, resolution, or time periods without checking.** Each of these mismatches can silently produce wrong results rather than an obvious error.
- **Undocumented downloads.** If you cannot remember where a file came from or when you got it, you cannot fully document or reproduce your project.

---

## Key takeaways

- Dataset choice shapes your question, method, workflow, and evaluation; use the selection checklist before committing.
- The four dataset categories above (imagery, vector/contextual, environmental, benchmark) cover most SDS320 project needs.
- The dataset table lists sources already used in course lessons as a starting point, not an exhaustive list.
- Documenting every dataset you use, as you use it, is part of a reproducible project, not an afterthought.
