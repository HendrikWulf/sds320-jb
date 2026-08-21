# L01 – Introduction to GeoAI

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
What GeoAI is and  why spatial data needs its own thinking & toolkit
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Context

You have already worked through the course orientation, set up your Python environment, and previewed the project handbook that will carry you from an idea to a finished SDS320 project. This lesson is where the technical spine of the course begins.

SDS320 builds on {abbr}`SDS210 (the prerequisite Spatial Data Science course)`. You already know how to work with raster and vector data, use Git, and run basic spatial analysis in Python. What you are missing, and what this lesson supplies, is a shared vocabulary for {term}`GeoAI`: what it is, why it is not just "computer vision with satellite pictures," and which of its core tasks might fit your own project.

Nothing here requires you to write code. The point of this lesson is conceptual footing. [L02 – Data acquisition](../02_data-acquisition.md) is where you start touching real datasets, and the concepts you build here will shape which data and which task type make sense for you to pursue.

---

## 2. Motivation

Every SDS320 project eventually has to answer a deceptively simple question: what, exactly, should the model predict? Getting this wrong is expensive. Teams that pick a task type before understanding their options often end up trying to force a segmentation problem into a classification workflow, or spend weeks labeling bounding boxes when a simpler scene-level label would have answered the research question just as well.

This lesson exists to help you avoid that. By the end of it, you should be able to look at a rough project idea and immediately narrow it down to a small set of plausible task types and tools, instead of discovering the mismatch three lessons from now when it is much harder to change course.

---

## 3. Learning objectives

By the end of this lesson, you should be able to:

- explain what GeoAI means in the context of spatial data science,
- describe why spatial data require different thinking from ordinary images,
- identify major application areas where GeoAI is useful,
- recognise the main Python tools used in GeoAI workflows,
- distinguish core GeoAI task types and match them to project questions,
- explain why foundation models are changing GeoAI workflows,
- draft a first GeoAI framing statement for your own SDS320 project.

---

## 4. Lesson roadmap

1. [What is GeoAI?](L01/01_what-is-geoai.md) — a working definition and a short history from manual photo interpretation to foundation models.
2. [Why spatial data is different](L01/02_why-spatial-data-is-different.md) — the properties of spatial data that make GeoAI its own field rather than a subfield of ordinary computer vision.
3. [Applications of GeoAI](L01/03_applications-of-geoai.md) — a tour of six domains where GeoAI is used in production, as inspiration for your own project.
4. [The Python ecosystem for GeoAI](L01/04_python-ecosystem.md) — the libraries you will use throughout SDS320, and how they fit together.
5. [Core GeoAI tasks](L01/05_core-geoai-tasks.md) — the seven task types that define almost every GeoAI problem, and how to tell them apart.
6. [From traditional methods to foundation models](L01/06_foundation-models.md) — how the field evolved, and what that means for how much data and compute your project actually needs.
7. [Project transfer](L01/07_project-transfer.md) — turning everything above into a first concrete direction for your own project.

---

## 5. Project framing

As you read through this lesson, keep your own project loosely in mind. You do not need a final idea yet, but a few questions are worth carrying with you:

- Which application domain from this lesson is closest to something you would actually want to investigate?
- What decision, question, or pattern would your project help someone understand or act on?
- Looking at the seven core tasks, which one or two seem closest to what you would want a model to output?
- Does your rough idea depend on very fine spatial detail, a long time series, or many spectral bands? Those choices will shape which data sources are realistic later.

---

## 6. Before class

- Skim [Chapter 1 of the course textbook](https://www.eoportal.org/satellite-missions/copernicus-sentinel-2) <!-- TODO: replace with the correct link to the assigned textbook chapter once available in the course reference section --> on the introduction to GeoAI, if you have not already.
- Re-open the [Project handbook](../03_project.md) pages on the research question and workflow design. You will revisit them at the end of this lesson.
- Confirm your Python environment from [Setup](../02_setup.md) still activates without errors. You will not need it for this lesson, but you will for the next one.
- Jot down one or two rough areas you are curious about (a place, a hazard, a resource, a process) even if you have no idea yet whether GeoAI is the right tool for them.

---

## 7. After this lesson

You should be able to explain, in your own words, what makes a dataset "spatial" in a way that matters for modeling, and you should be able to name the seven core GeoAI tasks without looking them up. For your own project, you should have a short written sketch: a candidate domain, a rough research direction, and a tentative task type, produced on the [project transfer](L01/07_project-transfer.md) page. This sketch is not a commitment. [L02 – Data acquisition](../02_data-acquisition.md) will test it against what data is actually available, and you may well revise it.