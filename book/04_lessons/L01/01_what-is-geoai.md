---
site:
  outline_maxdepth: 2
---

# What is GeoAI?

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Locating GeoAI between GIS, remote sensing, and machine learning
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. A working definition

{term}`GeoAI` (geospatial artificial intelligence) is the application of {term}`Deep Learning` and other machine learning methods to geospatial data — {term}`Remote Sensing` imagery, {term}`Vector Data`, elevation models, and similar sources — to detect, classify, segment, translate, or predict spatial patterns.

It is not a single tool or package. It is a practice that combines three things that used to live in separate communities:

| Field | Core question | Typical output |
| --- | --- | --- |
| Geographic information science (GIS) | Where is it, and how does it relate to other locations? | Maps, spatial queries, overlays |
| Remote sensing | What can we observe from imagery? | Classified scenes, indices, time series |
| Machine learning | What pattern predicts this outcome? | Trained models, predictions |

GeoAI sits at the overlap of all three: it uses machine learning models, trained and evaluated with the same rigour as any ML project, but applied to data that carries geographic meaning and needs geographic handling.

---

## 2. How it differs from "just GIS"

Traditional GIS analysis is often rule-based: you buffer a road, intersect two layers, or reclassify a raster using thresholds you define. The logic is explicit and the outcome is predictable from the rules.

GeoAI instead learns the mapping from data to outcome. Instead of writing "a building is a group of pixels with these reflectance values", you show a model many labelled examples of buildings and let it learn the pattern. This trades explicit control for the ability to recognise patterns too complex or too inconsistent to hand-code — but it also introduces new failure modes: models can be confidently wrong, need representative training data, and require evaluation just like any statistical method.

```{admonition} GeoAI does not replace GIS thinking
:class: important
Coordinate reference systems, resolution, scale, and data quality checks still matter just as much in a GeoAI workflow as in a traditional GIS one. A powerful model on badly prepared spatial data will still produce a badly prepared answer.
```

---

## 3. How it differs from "just machine learning"

Generic machine learning tooling is usually built around independent, identically distributed rows in a table. Geospatial data breaks that assumption in several ways covered in the [next page](02_why-spatial-data-is-different.md) — nearby locations are correlated, resolution changes what a model can detect, and a raster is really a georeferenced grid, not just an array of numbers.

This means GeoAI work usually needs geospatial-aware tooling on top of standard machine learning frameworks: libraries that understand {term}`Coordinate Reference System (CRS)` metadata, can tile large rasters for training, and can turn model outputs back into georeferenced layers you can map. The [Python ecosystem page](04_python-ecosystem.md) introduces the specific packages this book uses for that.

---

## 4. A first mental model

A useful way to think about a GeoAI project is as a pipeline:

```text
Find and access data → Prepare and check data → Prepare or find training data →
Choose a task and model → Train or apply a model → Interpret and communicate results
```

Every lesson in this book maps onto one or more stages of this pipeline. This lesson (L01) sits before all of them — it is about understanding the field well enough to navigate the rest of the pipeline with intention rather than by trial and error.

---

## 5. Key takeaways

* {term}`GeoAI` combines GIS thinking, remote sensing data, and machine learning methods.
* It replaces explicit, rule-based logic with learned patterns — powerful, but it needs careful data and evaluation.
* Geospatial data has properties that plain machine learning tooling does not handle by default.
* A GeoAI project is a pipeline: data, preparation, training data, modelling, and interpretation, all covered across later lessons in this book.
