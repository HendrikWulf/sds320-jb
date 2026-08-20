---
site:
  outline_maxdepth: 2
---

# Applications of GeoAI

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Where GeoAI is already used, and what kind of question each application answers
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why look at applications before methods

Methods are easier to learn once you know what problem they solve. This page surveys established application areas so you can recognise which family of {term}`GeoAI` task — covered in more depth on the [core GeoAI tasks page](05_core-geoai-tasks.md) — fits a given real-world question.

---

## 2. Environmental and land monitoring

* **Land cover and land use mapping** — classifying an area into categories such as forest, cropland, water, or built-up land, often at national or global scale.
* **Deforestation and forest change monitoring** — detecting canopy loss over time from repeated satellite observations, an example of {term}`Change Detection`.
* **Water and coastal monitoring** — tracking surface water extent, algal blooms, or coastline change.

## 3. Urban and infrastructure analysis

* **Building footprint extraction** — automatically delineating buildings from aerial or satellite imagery, useful when official cadastral data is missing or outdated.
* **Road and infrastructure mapping** — extracting road networks from imagery, often combined with {term}`OpenStreetMap (OSM)` data for validation.
* **Urban growth monitoring** — comparing built-up area over multiple years to quantify expansion.

## 4. Agriculture

* **Crop type classification** — identifying which crop is grown in a field from multispectral or time-series imagery.
* **Yield estimation and crop health monitoring** — using spectral indices and {term}`Regression` models to estimate productivity or stress before harvest.
* **Field boundary delineation** — automatically outlining agricultural field boundaries, a task with active open benchmarks and machine-learning-derived global datasets.

## 5. Disaster response and humanitarian mapping

* **Damage assessment** — comparing pre- and post-event imagery to flag damaged buildings or infrastructure after earthquakes, floods, or storms.
* **Rapid mapping** — quickly extracting roads, buildings, or flood extent from newly released imagery to support relief efforts.

```{admonition} Data availability shapes what is possible
:class: caution
Disaster-response applications are often limited by data availability and licensing, not by modelling capability. Event-based imagery collections may only exist for select, high-profile events — a limitation the [data acquisition lesson](../02_data-acquisition.md) covers directly.
```

## 6. Biodiversity and conservation

* **Habitat mapping** — classifying vegetation or habitat types from imagery to support conservation planning.
* **Species and structure detection** — for example, individual tree crown detection or wildlife counting from aerial imagery.

## 7. Climate and disaster risk

* **Flood and hazard susceptibility mapping** — combining terrain, land cover, and historical event data to estimate risk.
* **Canopy height and biomass estimation** — using {term}`Pixel-Level Regression` on remote sensing data to estimate structural variables relevant to carbon accounting.

---

## 8. Key takeaways

* GeoAI applications span environmental monitoring, urban analysis, agriculture, disaster response, biodiversity, and climate risk.
* Each application area typically maps onto one of a small number of core task families — detection, segmentation, change, translation, or regression.
* Data availability and licensing, not just modelling technique, often determine what is actually achievable in a given application.
* Use this page to locate your own project interest within an established application area before choosing a specific method.
