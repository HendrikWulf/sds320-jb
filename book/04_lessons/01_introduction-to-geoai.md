# L01 – Introduction to GeoAI

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
What GeoAI is, why spatial data needs its own thinking, and where this book is headed
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

This is the first lesson in the book. Before you search for data, build maps or train a model, you need a shared vocabulary and a mental map of the field: what {term}`GeoAI` actually is, why spatial data behaves differently from a typical tabular dataset, and which tools in the Python ecosystem you will rely on throughout the rest of the course.

This lesson does not teach a specific technique yet. It builds the conceptual foundation that the following lessons — data acquisition, interactive mapping, preprocessing, and the modelling lessons from object detection onward — all build on.

---

## 2. Motivation

Artificial intelligence is increasingly applied to satellite imagery, aerial photos, and geospatial vector data: mapping buildings after a disaster, tracking deforestation, monitoring crops, or detecting change in cities over time. These applications sit at the intersection of two fields that historically developed separately — geographic information science and machine learning.

That intersection is not a simple sum of the two. Spatial data has properties that most machine learning tools were never designed for: locations are not independent of their neighbours, resolution and scale change what a model can "see", and a coordinate reference system determines whether measurements are even meaningful. GeoAI is the emerging practice of applying AI methods to geospatial problems while taking these properties seriously.

```{admonition} A fast-moving field
:class: note
GeoAI is young and evolving quickly. Tools, packages, and even best practices from a year ago may already have shifted. Treat this book as an entry point and orientation, not a final reference — the [platforms and reference pages](../06_reference.md) later in this book point you toward resources that stay current.
```

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

* explain what {term}`GeoAI` is and how it differs from conventional GIS analysis and from generic machine learning,
* describe at least two properties that make spatial data different from tabular data,
* name real-world application areas where GeoAI is used,
* recognise the main Python packages this course relies on and what each one is for,
* describe the core families of GeoAI tasks covered later in this book,
* explain, at a conceptual level, what a foundation model is and why it matters for GeoAI,
* connect these ideas to your own project idea.

---

## 4. Lesson roadmap

Work through the pages in this order:

1. [What is GeoAI?](L01/01_what-is-geoai.md) — define the field and see how it relates to GIS, remote sensing, and machine learning.
2. [Why spatial data is different](L01/02_why-spatial-data-is-different.md) — spatial autocorrelation, scale, resolution, and coordinate reference systems.
3. [Applications of GeoAI](L01/03_applications-of-geoai.md) — real projects across environmental monitoring, urban analysis, agriculture, and disaster response.
4. [The Python ecosystem](L01/04_python-ecosystem.md) — the packages you will use throughout this book, and how they fit together.
5. [Core GeoAI tasks](L01/05_core-geoai-tasks.md) — a map of the task families covered in later lessons: detection, segmentation, translation, change, regression.
6. [Foundation models](L01/06_foundation-models.md) — what pretrained, general-purpose models mean for geospatial work.
7. [Project transfer](L01/07_project-transfer.md) — connect these ideas to your own project idea.

---

## 5. Project framing

You do not need a finished project idea yet, but keep these questions in mind as you read:

* What real-world question or phenomenon am I curious about, and does it have a spatial dimension?
* Would I expect a satellite image, an aerial photo, or a vector dataset to be more relevant to that question?
* Which of the application areas in this lesson feels closest to what I want to work on?
* Is my interest closer to "detect specific objects", "classify area", "measure change over time", or "estimate a continuous quantity"?

```{tip}
It is normal not to know the answer to all of these yet. The goal of this lesson is to give you enough vocabulary to start narrowing things down, not to lock in a final decision.
```

---

## 6. Before class

Before class, prepare the following:

* [ ] Skim the [Python & project setup](../02_setup.md) section if you have not already, so your environment is ready.
* [ ] Think of one real-world phenomenon you would like to study with spatial data.
* [ ] Bring one question or point of confusion about "AI and maps" to discuss in class.

---

## 7. After this lesson

After completing this lesson, you should have:

* a working definition of {term}`GeoAI` in your own words,
* a short list of application areas that could match your project interest,
* a rough sense of which core GeoAI task family (detection, segmentation, translation, change, regression) fits your idea best,
* familiarity with the names of the main Python packages used later in this book.

This lesson does not require any data download or coding. The next lesson, [L02 – Data acquisition](02_data-acquisition.md), is where you start working hands-on.
