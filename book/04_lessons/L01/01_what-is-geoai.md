---
site:
  outline_maxdepth: 2
---

# What is GeoAI?

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
A working definition of GeoAI and a short history of how the field got here
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Motivation

"GeoAI" gets used loosely. Sometimes it means any machine learning model that happens to touch a map. Sometimes it means only deep learning on satellite imagery. If you do not pin the term down early, it becomes hard to scope a project, because you will not know whether a random forest on tabular census data counts, or whether it has to involve a neural network and a GPU.

Having a clear definition also helps you talk about your own project precisely, both to your instructor and in your final report.

---

## 2. Core idea

{term}`GeoAI` is what happens where {term}`geospatial data science <GeoAI>` and artificial intelligence overlap. It combines domain knowledge about geography and {term}`remote sensing` with the pattern-recognition abilities of modern machine learning and deep learning. In practice, that means taking data that is tied to a location on Earth and using a model, rather than only manual rules or visual interpretation, to extract information from it.

The name is fairly recent. It became common in the mid-2010s as researchers began applying {term}`deep learning` to spatial problems at scale, and it was picked up by workshops and review papers that tried to formalize the overlap between AI and geospatial science. The underlying idea, using computation to pull information out of spatial data, is much older than the name.

---

## 3. How GeoAI developed

The idea behind GeoAI is older than the name. You can think of its development as a series of overlapping stages.

### A. Manual photo interpretation (1940s–1960s)

Trained analysts examined aerial photographs by eye to identify roads, buildings, and crop types. This worked, but it was slow, expert-dependent, and could take weeks to cover even a modest area. There was no automation at all; every judgment was made by a person.

### B. The satellite era and statistical classifiers (1970s–1980s)

The launch of Landsat 1 in 1972 gave scientists repeated, consistent observations of the Earth's surface for the first time. That created a new problem: far more imagery than people could look at by hand. Early automated approaches used simple statistical classifiers, assigning each pixel to a land-cover class based on its spectral signature, without any concept of what a "building" or "field" actually looked like as a shape.

### C. Traditional machine learning (1990s–2010s)

Methods such as {term}`random forests <Random Forest>`, support vector machines, and gradient boosting began to outperform the older statistical classifiers. They could handle more input variables and capture non-linear relationships. The catch is that a person still had to hand-design the input features, spectral indices, texture measures, shape metrics, that the model would learn from. This step, called feature engineering, took real domain expertise.

### D. The deep learning shift (2012 onward)

Starting with breakthroughs in general image classification around 2012, {term}`convolutional neural networks <Convolutional Neural Network (CNN)>` (CNNs) learned useful features directly from raw imagery, without a person hand-designing them. Architectures such as {term}`U-Net` became a default choice for pixel-level tasks like {term}`semantic segmentation <Semantic Segmentation>`, and the geospatial community adopted them quickly for land cover mapping, building extraction, and crop classification.

### E. Foundation models (2020s onward)

The current frontier is {term}`foundation models <Foundation Model>`: large models pre-trained on huge, broad datasets that can be adapted to many downstream tasks with comparatively little extra training. Meta's {term}`Segment Anything Model (SAM)` is a well-known general-purpose example, and geospatial-specific foundation models now exist for satellite imagery. These models can generalize across geographies and sensors in ways that earlier, narrowly-trained models could not, which lowers the amount of labeled data a new project needs to get started.

```{admonition} A carpenter analogy
:class: tip
Each stage in this history did not erase the one before it. Traditional machine learning is still a reasonable choice when labeled data is scarce or when you need an interpretable model. Choosing the right era's tool for your problem, rather than automatically reaching for the newest one, is itself a skill you will practice in this course.
```

---

## 4. Python reactivation

Nothing here requires code yet. But it is worth noting where your SDS210 skills fit: the {term}`raster <Raster Data>` and {term}`vector <Vector Data>` handling you already know (reading a {term}`GeoTIFF`, working with a {term}`GeoDataFrame`) sits underneath every stage above. GeoAI does not replace that foundation; it adds a modeling layer, built on packages such as {term}`PyTorch`, on top of it. You will see exactly how those packages fit together on the [Python ecosystem](04_python-ecosystem.md) page.

---

## 5. Common pitfalls

- **Treating "GeoAI" as a synonym for "deep learning on satellite images."** Traditional machine learning on spatial data is still GeoAI, and is sometimes the more appropriate choice for your project.
- **Assuming newer always means better.** A foundation model can be the wrong tool if your problem is small, well-understood, and better served by an interpretable model with a handful of hand-picked features.
- **Confusing scene-level labels with pixel-level maps.** As you will see on the [core tasks](05_core-geoai-tasks.md) page, these are genuinely different tasks with different data requirements, not two names for the same thing.

---

## 6. Mini task

Pick one real-world GeoAI system, either one named on this page (Microsoft's building footprints, Google's Dynamic World, Meta's SAM) or one you find yourself. In three to four sentences:

1. Describe what problem it solves.
2. Say which stage of the history above it belongs to (traditional ML, CNN-era deep learning, or foundation model).
3. Justify your placement using one concrete detail about how the system works.

:::{note} Sample solution
:class: dropdown

Google's Dynamic World project classifies global land cover in near-real time, updating every time a new Sentinel-2 image becomes available. It belongs to the CNN-era deep learning stage rather than the foundation-model stage, because it is a task-specific segmentation model trained to output a fixed set of land-cover classes, not a general-purpose model adapted afterward to many different tasks. The near-real-time updating is only possible because the model runs automatically on new imagery without a person manually reviewing each output, which distinguishes it from the manual photo-interpretation era.
:::

---

## 7. Key takeaways

- {term}`GeoAI` is the overlap of geospatial data science and artificial intelligence; it is not limited to deep learning or to satellite imagery.
- The field moved through five broad stages: manual interpretation, statistical classifiers, traditional machine learning, deep learning, and foundation models.
- Each stage lowered the barrier to entry, but did not make earlier approaches obsolete.
- Being able to place a system or project idea within this history helps you reason about what tools and how much labeled data it will realistically need.
