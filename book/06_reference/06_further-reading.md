---
site:
  outline_maxdepth: 2
---

# Further reading

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Selected resources for going deeper
</div>
<!-- markdownlint-enable MD033 -->

---

## How to use this page

Start with the course materials. The lessons, cheatsheets, and other reference pages are written specifically for this course and its workflows. Use the links below selectively, when you want to go deeper on a specific tool or idea than a lesson had room for, not as a replacement for the course material itself.

This list is intentionally curated rather than exhaustive: mostly official documentation, plus a handful of stable educational resources you can realistically return to during your project.

---

## Python and Jupyter

- [Python Tutorial](https://docs.python.org/3/tutorial/) — the official introduction and reference refresher for core Python.
- [Project Jupyter Documentation](https://docs.jupyter.org/en/stable/) — an overview of notebooks, kernels, JupyterLab, and the wider Jupyter ecosystem.
- [JupyterLab User Guide](https://jupyterlab.readthedocs.io/en/stable/user/) — practical guidance for working with notebooks, files, terminals, and the JupyterLab interface.
- [NumPy User Guide](https://numpy.org/doc/stable/user/) — arrays, indexing, and broadcasting, concepts that recur throughout raster and GeoAI workflows.
- [pandas Getting Started](https://pandas.pydata.org/docs/getting_started/) — a refresher for DataFrames, data manipulation, and tabular workflows.

---

## Geospatial Python

- [GeoPandas Documentation](https://geopandas.org/en/stable/docs.html) — the main reference for vector data, spatial joins, projections, and overlays.
- [Rasterio Quickstart](https://rasterio.readthedocs.io/en/stable/quickstart.html) — a concise introduction to reading, writing, and working with raster data in Python.
- [Xarray Documentation](https://docs.xarray.dev/en/stable/) — labeled, multi-dimensional arrays, especially useful for multi-band and time-series raster data.
- [rioxarray Documentation](https://corteva.github.io/rioxarray/stable/) — adds CRS handling, reprojection, and clipping to Xarray.
- [Leafmap Documentation](https://leafmap.org/) — interactive mapping and raster/vector exploration used throughout this course.
- [Shapely User Manual](https://shapely.readthedocs.io/en/stable/manual.html) — geometry creation, predicates, intersections, and buffers.
- [pyproj Documentation](https://pyproj4.github.io/pyproj/stable/index.html) — coordinate reference systems and coordinate transformations in Python.

---

## Remote sensing and spatial data

- [Geographic Data Science with Python](https://geographicdata.science/book/) — a free, open textbook covering spatial data science methods and workflows.
- [NASA ARSET (Applied Remote Sensing Training)](https://appliedsciences.nasa.gov/what-we-do/capacity-building/arset/about-arset) — free training covering remote sensing fundamentals and applications.
- [USGS Landsat Missions](https://www.usgs.gov/landsat-missions) — authoritative information on Landsat sensors, products, and the long-term archive.
- [Copernicus Data Space Ecosystem Documentation](https://documentation.dataspace.copernicus.eu/) — Sentinel data, catalog access, and APIs.
- [Microsoft Planetary Computer Documentation](https://planetarycomputer.microsoft.com/docs) — particularly useful for STAC-based discovery and programmatic access to Earth observation data.
- [STAC (SpatioTemporal Asset Catalog)](https://stacspec.org/en/) — the metadata specification behind Planetary Computer and many other geospatial data catalogs.

---

## Machine learning and deep learning

- [scikit-learn User Guide](https://scikit-learn.org/stable/user_guide.html) — the standard reference for classical machine learning, including the classifiers used in the embeddings lesson.
- [PyTorch Tutorials](https://docs.pytorch.org/tutorials/) — official tutorials covering tensors, model construction, training, and data loading.
- [Torchvision Documentation](https://docs.pytorch.org/vision/stable/index.html) — relevant for the classification, detection, and segmentation architectures used throughout this course.
- [Dive into Deep Learning](https://d2l.ai/) — a free, interactive textbook covering deep learning concepts with runnable code.
- [Hugging Face Computer Vision Course](https://huggingface.co/learn/computer-vision-course/en/unit0/welcome/welcome) — accessible material on CNNs, vision transformers, and generative and zero-shot computer vision models.

---

## GeoAI and foundation models

- [geoai documentation](https://opengeoai.org) — documentation and worked examples for the `geoai` package used throughout this course's method lessons.
- [TorchGeo Documentation](https://docs.torchgeo.org/en/stable/) — datasets, samplers, and pre-trained models for geospatial deep learning in PyTorch.
- [segment-geospatial (samgeo) Documentation](https://samgeo.gishub.org/) — documentation for the package bridging Segment Anything models to georeferenced data.
- [Clay Foundation Model](https://github.com/Clay-foundation/model) — the open-source Earth observation foundation model used in the satellite embeddings lesson.
- [Google Satellite Embedding (AlphaEarth) dataset](https://developers.google.com/earth-engine/datasets/catalog/GOOGLE_SATELLITE_EMBEDDING_V1_ANNUAL) — the official Earth Engine catalog entry for the annual embedding dataset used in the embeddings lesson.
- [Hugging Face Hub — Models](https://huggingface.co/docs/hub/models) — useful for finding, documenting, and reusing pre-trained models, including several used in this course.

---

## Writing, reproducibility and project work

- [The Turing Way — Guide for Reproducible Research](https://book.the-turing-way.org/reproducible-research/reproducible-research/) — a broader reference on reproducible computational research, version control, and open research.
- [The Turing Way — Research Compendia](https://book.the-turing-way.org/reproducible-research/compendia) — treats code, data, documentation, and outputs as parts of one reproducible project, directly relevant to how you should structure your SDS320 repository.
- [GitHub Docs — About READMEs](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-readmes) — concise guidance on what a repository README should communicate.
- [Markdown Guide](https://www.markdownguide.org/) — a clear reference for Markdown syntax, useful for READMEs and notebook narratives.
- [Zenodo](https://zenodo.org/) — a free service for archiving a project repository and obtaining a citable DOI.
- [FAIR Principles — GO FAIR](https://www.go-fair.org/fair-principles/) — an overview of making research data Findable, Accessible, Interoperable, and Reusable.
