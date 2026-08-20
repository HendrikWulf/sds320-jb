---
site:
  outline_maxdepth: 2
---

# Project transfer

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Turning this lesson into concrete progress on your SDS320 project
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why this page matters

This lesson should change the state of your project. After working through it, you should no longer only have a topic idea. You should have a first data acquisition plan: candidate datasets, search criteria, access routes, limitations and a small test.

The goal is not to download everything. The goal is to know whether your project can move forward with realistic data.

---

## 2. Project checklist

Apply this checklist to your own project.

### Project fit

- [ ] I have a draft research question.
- [ ] I know the study area or candidate area.
- [ ] I know the time period or observation date I need.
- [ ] I can explain what the data must show.

### Raster data

- [ ] I identified at least one remote sensing or raster dataset.
- [ ] I checked spatial resolution.
- [ ] I checked temporal coverage.
- [ ] I checked useful bands or variables.
- [ ] I checked likely quality issues such as clouds, gaps or NoData values.
- [ ] I tested a small search or download where possible.

### Vector and contextual data

- [ ] I identified at least one vector or contextual dataset.
- [ ] I know what role it plays: context, labels, validation, interpretation or visualisation.
- [ ] I checked coverage and geometry type.
- [ ] I checked whether it aligns conceptually with my raster data or question.

### Documentation

- [ ] I recorded provider and access route.
- [ ] I recorded licence or attribution notes.
- [ ] I recorded format and CRS where available.
- [ ] I recorded the main limitation.
- [ ] I saved this information in my repository or project notes.

---

## 3. Decision points

### Which dataset is the main input?

A reasonable main dataset is the one that most directly supports your research question. It should cover your area and time period and have a resolution suitable for your target feature or pattern.

### Which dataset is only context?

A context dataset helps interpret results but does not answer the research question on its own. Examples include roads, boundaries, buildings or land-use layers.

### What is the smallest useful test?

A useful first test could be:

- one small bounding box,
- one scene,
- one vector layer,
- one date range,
- one band combination,
- one map overlay.

Start with the smallest test that can reveal whether the data are promising.

### What should be simplified?

Simplify if the data are too large, too hard to access, too cloudy, too incomplete or too poorly documented.

Possible simplifications:

- smaller study area,
- shorter time period,
- fewer datasets,
- fewer classes,
- coarser but easier data,
- one representative case study,
- simpler method.

---

## 4. Common pitfalls

| Pitfall | How to avoid it |
| --- | --- |
| You cannot name the main dataset | Return to the research question and identify what evidence is needed. |
| The data do not cover your study area | Change data source or reduce the study area. |
| The time period does not match the question | Adjust the question or find another archive. |
| The target feature is not visible | Use higher-resolution data or change the task. |
| Data access requires too many manual steps | Document the steps or choose a simpler source. |
| The dataset is too large for your computer | Test a smaller bounding box or lower-resolution option. |
| Licence or attribution is unclear | Do not publish outputs until this is checked. |
| You have data but no evaluation idea | Identify reference data, visual checks or comparison strategy. |

---

## 5. Mini deliverable

Create a **data acquisition plan** for your SDS320 project.

Save it as one of the following:

```text
data/data_inventory.md
data/data_inventory.csv
notebooks/01_data_search.ipynb
````

Use this structure:

```text
Project question:
Study area:
Time period:

Main raster dataset:
Provider:
Access route:
Search criteria:
Useful assets or bands:
Expected output:
Main limitation:

Main vector/context dataset:
Provider:
Access route:
Role in project:
Main limitation:

Small test:
What I tested:
What worked:
What did not work:
Next decision:
```

This mini deliverable should be concrete enough that you can discuss it in class or use it as the basis for the next project step.

---

## 6. Reflection questions

Answer these briefly for your own project:

1. Which dataset is most central to answering my research question?
2. What is the main data risk in my project?
3. What is the smallest test that can reveal whether the data are usable?
4. Which licence, attribution or sharing issue do I still need to check?
5. What will I simplify if data access becomes harder than expected?

---

## 7. Key takeaways

- Data acquisition should produce decisions, not only files.
- A small test is better than a large unverified download.
- Your project needs both data fit and documentation.
- Raster and vector data should have clear roles in the workflow.
- The next project step is to turn this plan into preprocessing and analysis.
