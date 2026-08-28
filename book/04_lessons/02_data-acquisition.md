# L02 - Data acquisition

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Finding, checking, documenting and acquiring spatial data for your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

In the first project weeks, you move from a broad topic toward a feasible spatial question. This lesson focuses on the next critical step: finding data that can actually answer that question.

In SDS, data acquisition is not only downloading files. It includes searching for suitable datasets, checking whether they match your area and time period, understanding formats and licences, inspecting quality, and documenting what you selected.

This lesson sits between project planning and preprocessing. After this lesson, you should be able to create a first data inventory and decide whether your project idea is realistic with the data you can access.

---

## 2. Motivation

Many spatial data science projects fail because the data decision is made too late. A method may sound promising, but it only works if the required data exist, are accessible, have suitable resolution and can be processed in time.

For GeoAI projects, this matters even more. {term}`Remote Sensing` data differ in spatial resolution, spectral bands, temporal coverage, cloud cover, licensing and file size. {term}`Vector Data` can provide context, training labels, validation data or features for interpretation, but it also needs careful checking.

So, the big project question is:

```text
Which data are suitable enough for my question, method, output and time budget?
```

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

* search for spatial datasets using project-specific criteria,
* compare remote sensing data sources for spatial, temporal and spectral fit,
* retrieve or plan access to raster and vector data for a defined area of interest,
* document data sources, licences, access routes and limitations,
* create a first data acquisition plan for your SDS320 project.

---

## 4. Lesson roadmap

Work through the pages in this order:

1. [Finding suitable spatial data](L02/01_data-search.md) — define what “suitable data” means for your project and create a first data inventory.
2. [Working with remote sensing data](L02/02_remote-sensing-data.md) — use STAC-based search logic to find imagery such as Sentinel-2, Landsat, NAIP or SWISSIMAGE.
3. [Adding vector and contextual data](L02/03_vector-context-data.md) — explore vector sources such as Overture Maps and other context data that can support your analysis.
4. [Citing and documenting data](L02/04_data-citation.md) — record data provenance, licences, limitations and reuse conditions.
5. [Project transfer](L02/05_project-transfer.md) — turn the lesson into a concrete data acquisition plan for your own project.

---

## 5. Project framing

As you read, keep your own project in mind.

Use these guiding questions:

* Which dataset could answer my research question most directly?
* Does the dataset cover my study area and time period?
* Are the spatial resolution, spectral bands and file size realistic for my method?
* What will I need to document so that another student can understand and reproduce my data choices?

```{tip}
Do not collect data only because it is available. Collect data because it supports a specific project decision.
```

---

## 6. Before class

Before class, prepare the following:

* [ ] Bring one draft research question or topic idea.
* [ ] Define a rough {term}`Area of Interest (AOI)` for your project.
* [ ] Identify at least one possible raster data source.
* [ ] Identify at least one possible vector or contextual data source.
* [ ] Create a project folder where you can store notes, data inventories and test downloads.
* [ ] Be ready to explain one uncertainty about your data access.

---

## 7. After this lesson

After completing this lesson, you should have:

* a first data inventory,
* at least one candidate raster dataset,
* at least one candidate vector or contextual dataset,
* notes on licence, access route, spatial coverage and temporal coverage,
* one small data access or inspection test,
* a decision about whether your project scope is still feasible.

This does not need to be final. The goal is to move from “I might use this data” to “I know what I need to check next.”
