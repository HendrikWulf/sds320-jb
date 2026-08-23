---
site:
  outline_maxdepth: 2
---

# Applications of GeoAI

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Six domains where GeoAI is already used in production, as inspiration for your own project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why these examples matter

Reading about real, deployed systems is one of the fastest ways to find a project direction. Each example below also gives you a concrete answer to a question you will need for your own project: which of the seven core tasks does this system rely on, and why that one rather than an alternative.

---

## 2. Core idea

GeoAI systems tend to cluster around a small number of task types (which you will meet formally on the [core tasks page](05_core-geoai-tasks.md)) applied to domain-specific problems. Recognizing the domain-to-task pattern is a useful shortcut when you are scoping your own project.

---

## 3. Application domains

Here is a synthesized version that combines the engaging, real-world examples of the second text with the clear, project-oriented structure of the first. I have formatted the research questions as blockquotes to better suit a textbook or documentation layout.

### Urban planning and smart cities

Automatically extracting infrastructure and land use from imagery is now common at a global scale; Microsoft's building footprint dataset and Google's Open Buildings project both used deep learning to map hundreds of millions of structures. Beyond static mapping, {term}`change detection <Change Detection>` models track urban expansion and informal settlement growth over time, while {term}`object detection <Object Detection>` supports tasks like counting vehicles or estimating parking occupancy.

> "Where are buildings, roads, or impervious surfaces visible?"
> "How has the built-up area changed over time?"

* **Possible data:** Aerial imagery, satellite imagery, building footprints, road networks, land-use maps, and administrative boundaries.
* **Common tasks:** {term}`Object Detection`, {term}`Instance Segmentation`, semantic segmentation, and change detection.
* **Useful outputs:** Building maps, land-cover maps, change maps, density estimates, or district-level summaries.

### Agriculture and food security

Agricultural applications often use repeated satellite observations to monitor crops. Crop type mapping uses {term}`semantic segmentation <Semantic Segmentation>` and classification on time series to identify what is growing in each field. Yield prediction is typically framed as {term}`regression <Regression>`, combining vegetation indices, weather, and soil data to forecast harvest outcomes. Furthermore, precision agriculture utilizes imagery to detect within-field variability, targeting water or fertilizer much more precisely than uniform treatments.

> "Can crop types be distinguished from seasonal satellite patterns?"
> "Where does vegetation stress appear within a field or region?"

* **Possible data:** Sentinel-2, Landsat, radar data, weather data, field boundaries, and reference labels.
* **Common tasks:** Classification, {term}`Semantic Segmentation`, time-series analysis, and {term}`Regression`.
* **Useful outputs:** Crop-type maps, vegetation-index summaries, stress maps, or yield estimates.

### Environmental monitoring and conservation

Global Forest Watch tracks tree cover loss using machine learning on Landsat imagery, while newer systems fuse Landsat, Sentinel-2, and radar data to flag deforestation within days. Biodiversity monitoring uses similar {term}`object detection <Object Detection>` and classification approaches to count animals in drone imagery or map habitat types. Additionally, climate applications process large volumes of satellite records to quantify glacier retreat or estimate methane emissions from infrastructure.

> "Where has forest cover changed?"
> "Can habitat patterns be mapped from imagery and context data?"

* **Possible data:** Satellite time series, aerial imagery, protected-area boundaries, elevation data, land-cover products, and field observations.
* **Common tasks:** Change detection, segmentation, regression, and embeddings.
* **Useful outputs:** Change maps, habitat classifications, canopy-height estimates, or uncertainty discussions.

### Disaster response and humanitarian mapping

Comparing pre- and post-event imagery with {term}`change detection <Change Detection>` models supports rapid damage assessment after earthquakes or hurricanes. The xBD dataset, one of the largest public datasets for this task, covers over 850,000 building annotations across 19 disaster events. {abbr}`SAR (Synthetic Aperture Radar)` imagery, which can see through clouds, is commonly used for near real-time flood mapping, as optical sensors are often blocked by storm systems exactly when data is most needed.

> "Where is flood extent visible after an event?"
> "Which buildings may have changed or been damaged?"

* **Possible data:** Pre- and post-event satellite imagery, radar imagery, building footprints, roads, event boundaries, and humanitarian reference data.
* **Common tasks:** {term}`Change Detection`, object detection, segmentation, and damage classification.
* **Useful outputs:** Flood maps, building-damage maps, affected-area summaries, or visual comparisons.

### Water resources management

Deep learning models now support streamflow prediction and flood forecasting, in some cases reaching several days of useful lead time even in ungauged watersheds. {term}`Regression` models estimate water quality parameters, such as chlorophyll concentration, directly from imagery. Combined approaches also integrate satellite gravity measurements with land surface data to track groundwater storage changes.

> "Where has surface water expanded or contracted?"
> "Can satellite imagery estimate a specific water-quality indicator?"

* **Possible data:** Optical imagery, radar imagery, water masks, elevation data, catchment boundaries, and monitoring stations.
* **Common tasks:** Segmentation, change detection, and {term}`Regression`.
* **Useful outputs:** Water masks, time series, turbidity estimates, flood extent maps, or catchment summaries.

### Public health and environmental exposure

Vector-borne disease models combine satellite-derived environmental variables—like temperature, standing water, and vegetation—with case data to flag high-risk areas. Satellite-derived {abbr}`PM2.5 (fine particulate matter smaller than 2.5 micrometers)` datasets are actively used to estimate global air-pollution exposure. Furthermore, foundation-model pipelines that fuse atmospheric and Earth observation data can now produce fine-resolution urban heat maps for exposure assessment.

> "Where are urban heat patterns strongest?"
> "Which neighbourhoods are disproportionately exposed to environmental stressors?"

* **Possible data:** Land surface temperature, air-quality products, population grids, green-space layers, health-related boundaries, and urban morphology data.
* **Common tasks:** Regression, classification, spatial comparison, and visualization.
* **Useful outputs:** Exposure maps, risk surfaces, neighbourhood comparisons, or uncertainty-aware interpretations.

```{admonition} Spotting the pattern
:class: hint
As you read the list above, notice how often "compare two time periods" maps to change detection, "identify and locate individual objects" maps to detection, and "produce a continuous estimate" maps to regression. Practicing this mapping now will save you time on the [core tasks page](05_core-geoai-tasks.md).
```

---

## 4. Workflow

Use applications as models for project design.

### Step A: Identify the problem type

Ask whether the application is mainly about mapping, detecting, counting, comparing, predicting or monitoring.

### Step B: Identify the spatial evidence

Ask what data provide evidence. Is the key information in imagery, time series, vector layers, field labels, sensors or a combination?

### Step C: Identify the task type

Translate the problem into a task: classification, detection, segmentation, change detection, regression or embedding-based exploration.

### Step D: Identify the output

Name the final output. A project becomes clearer when you can name the map, figure, metric or table you want to produce.

### Step E: Identify the limitation

Every application has limitations. Look for issues such as resolution, incomplete labels, clouds, sensor differences, spatial bias, uncertainty or ethical concerns.

---

## 5. Python reactivation

No new Python here. This is a good moment to reactivate a slightly different skill: skimming source material for the specific detail you need (which dataset, which task, which scale) rather than reading start to finish. You will do the same thing on [L02 – Data acquisition](../02_data-acquisition.md) when comparing candidate data sources.

---

## 6. Common pitfalls

* **Picking a domain before a question.** "I want to do something with agriculture" is not yet a project. Pair the domain with a specific decision or pattern you want to understand.
* **Assuming global-scale examples are achievable at the same scale in a one-semester project.** These systems represent years of engineering effort. Scale your ambition to a study area and timeline you can actually finish.
* **Ignoring data availability for your region.** A method that works well globally may rely on data (fine-resolution commercial imagery, dense ground-truth labels) that is not freely available for your area of interest.

---

## 7. Mini task

Pick two of the six domains above. For each, name the specific product or dataset mentioned and identify which core task type it most likely relies on (you will formally meet all seven task types on the next page, so a reasonable guess based on the description is fine here).

:::{note} Sample solution
:class: dropdown

Domain: disaster response. Example: the xBD building-damage dataset. Likely task: change detection (comparing pre- and post-event imagery), closely paired with segmentation to isolate individual buildings before classifying their damage level.

Domain: agriculture. Example: crop-type mapping from satellite time series. Likely task: semantic segmentation, since the goal is a pixel-level class map (which crop is growing where), not a single label for the whole scene.
:::

---

## 8. Further reading

* [Microsoft Global Building Footprints](https://planetarycomputer.microsoft.com/dataset/ms-buildings)
* [Google Open Buildings](https://sites.research.google/gr/open-buildings)
* [Global Forest Watch](https://www.globalforestwatch.org)

---

## 9. Key takeaways

* GeoAI applications span urban planning, agriculture, environmental monitoring, disaster response, water resources, and public health, among other domains.
* Most real systems map cleanly onto one or two of the seven core GeoAI tasks, which makes them useful references when scoping your own project.
* Global, production-scale examples are useful inspiration but are not the right benchmark for what a one-semester student project should attempt.
* Data availability for your specific study area is a stronger constraint on feasibility than the domain itself.
