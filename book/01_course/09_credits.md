# Credits

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Acknowledging the people, open-source resources and AI tools
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. About this Jupyter Book

This Jupyter Book is in countinous development as teaching material for **SDS320 – Project**, part of the [Spatial Data Science curriculum](https://www.geo.uzh.ch/en/studying/prospective-students/minors/sds-minor.html) at the University of Zurich.

The book combines several kinds of contributions:

- original course design, explanations, exercises, project-oriented framing, examples and adaptations developed specifically for SDS320;
- ideas, workflows, code examples and technical foundations drawn from open-source geospatial and GeoAI resources;
- AI-assisted drafting, editing, restructuring and language refinement.

A central aim of this page is to make these contributions transparent.

Attribution is an important part of academic practice and open education. Open-source resources make it possible to build on existing methods, software and teaching materials, but they also require acknowledgement of where ideas and code originate.

---

## 2. GeoAI foundations

A substantial part of the **GeoAI methods, practical workflows, code patterns and lesson inspiration** in SDS320 builds on the open-source work of **Qiusheng Wu**, Associate Professor in the Department of Geography and Sustainability at the University of Tennessee, Knoxville. His work focuses on GeoAI, open-source geospatial software, geospatial data science and cloud computing.

In particular, SDS320 draws on resources developed around the `geoai` Python package and the broader GeoAI teaching ecosystem created by Wu. These include:

- the [`geoai` website and documentation](https://opengeoai.org/),
- the [GeoAI examples](https://opengeoai.org/examples/download_data/),
- the online book [*GeoAI with Python: A Practical Guide to Open-Source Geospatial AI*](https://book.opengeoai.org/),
- the [GeoAI Book GitHub repository](https://github.com/giswqs/GeoAI-Book),
- Wu's broader [GIS Hub](https://gishub.org/),
- the associated [GeoAI video tutorials and playlist](https://www.youtube.com/watch?v=VIl29Rca6zE&list=PLAxJ4-o7ZoPcvENqwaPa_QwbbkZ5sctZE),
- and the project's [citation guidance](https://opengeoai.org/citations/).

These resources provide important technical and conceptual foundations for the SDS320 lessons on image recognition, object detection, semantic segmentation, instance segmentation, image translation, change detection, pixel-level regression, Segment Anything, and satellite embeddings.

The SDS320 Jupyter Book is not a reproduction of the GeoAI book, but its source code has been selectively used, reorganized, adapted, expanded and reframed around the learning objectives and project-based structure of SDS320. The lesson architecture, links between methods and student projects, learning activities, reflection questions, project-transfer sections, explanations and course-specific examples were developed for the SDS320 context.

---

## 3. How to cite GeoAI

If you use the `geoai` package or directly build on workflows from the GeoAI materials in your own work, please cite the relevant original source.

### GeoAI Python package

> Wu, Q. (2026). GeoAI: A Python package for integrating artificial intelligence with geospatial data analysis and visualization. *Journal of Open Source Software, 11*(118), 9605. [https://doi.org/10.21105/joss.09605](https://doi.org/10.21105/joss.09605)

The paper is available through the [Journal of Open Source Software](https://joss.theoj.org/papers/10.21105/joss.09605).

### GeoAI book

> Wu, Q. (2026). *GeoAI with Python: A Practical Guide to Open-Source Geospatial AI*. Independently published. PDF edition ISBN 979-8993859729; Print edition ISBN 979-8253507414. Available at [https://book.opengeoai.org/](https://book.opengeoai.org/).

This is the citation recommended by the book itself.

Students who directly reuse GeoAI code or workflows in their SDS320 projects should cite the relevant GeoAI source in addition to documenting the datasets, software packages and other external resources used in their project.

---

## 4. Use of generative AI

Large language models were used as **writing and editing assistants** during the development of this Jupyter Book.

In particular, Claude Sonnet 5 was used to support tasks such as:

- revising explanatory text,
- improving structure and readability,
- revising explanations of code and methodological concepts,
- proposing alternative formulations,
- proofreading and language editing.

Technical documentation, publications, source code, datasets and other referenced materials remained the factual basis for the course content. AI-generated suggestions were reviewed, selected, edited and integrated into the Jupyter Book as part of the course-development process.

Where the material includes technical workflows or code, readers should therefore continue to consult the linked documentation and verify that examples work with the software versions and data used in their own environment.

---

## 5. Other sources

The GeoAI resources acknowledged above are an important foundation for the methods part of SDS320, but they are not the only external sources used in this Jupyter Book.

Individual lesson pages also draw on and link to:

- academic publications,
- software documentation,
- open-source repositories,
- geospatial datasets,
- model documentation,
- educational websites and tutorials.

These lesson-specific references complement the broader acknowledgement on this page.

More generally, SDS320 benefits from the work of the open-source Python, geospatial, remote-sensing and machine-learning communities. Packages and projects such as PyTorch, GeoPandas, Rasterio, TorchGeo, scikit-learn, Jupyter, `geoai`, `segment-geospatial` and many others make it possible to teach modern spatial data science using transparent and reproducible workflows.
