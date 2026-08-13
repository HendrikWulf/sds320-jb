---
site:
  outline_maxdepth: 2
---

# Figures and maps

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Communicating spatial results clearly and honestly
</div>
<!-- markdownlint-enable MD033 -->

---

## Why figures and maps matter

Figures and maps are not decoration. They are evidence for your project argument.

A good figure helps the reader understand what you analysed, what you found and why it matters. A weak figure can confuse the reader, hide uncertainty or make a result look more precise than it is.

The SDS110 visualisation material used a simple starting point: every visualisation should begin with a question. For SDS320, ask:

```text
What do I want the reader to learn from this figure?
Where is the result located?
Why does this visual matter?
Who is the audience?
```

---

## What makes a good project figure?

A good SDS320 figure or map usually:

- answers or supports the research question,
- focuses on one main message,
- has a clear caption,
- shows enough spatial context,
- uses a suitable {term}`Basemap` only when helpful,
- includes a legend, labels, units and time frame where needed,
- uses colour choices that match the data and do not mislead,
- is readable in both report and presentation formats,
- avoids unnecessary visual clutter,
- makes limitations visible or discusses them in the caption or text.

```{tip}
Resist the temptation to put everything on one map. A simpler figure that supports one clear message is usually stronger.
```

---

## Types of useful project visuals

Different project stages need different visuals.

| Visual type | Use it for |
| --- | --- |
| Overview map | Show the study area and spatial context. |
| Input data map | Show what the source data look like. |
| Preprocessing or quality-check figure | Show clipping, masking, cloud problems, missing data or alignment checks. |
| Workflow diagram | Explain the steps from raw data to result. |
| Model output map | Show predictions, detections, segmentation masks or regression outputs. |
| Comparison map | Compare dates, methods, classes, scenarios or before/after outputs. |
| Error or uncertainty figure | Show where predictions are uncertain or where errors occur. |
| Summary chart | Show counts, areas, class proportions, metric values or trends. |
| Key-result figure | Communicate the main answer to the research question. |

Not every project needs all of these. Choose figures that serve your argument.

---

## Map design checklist

Before using a map in your report or presentation, check:

- [ ] Does the map answer a specific question?
- [ ] Is the title or caption clear?
- [ ] Is the study area visible and understandable?
- [ ] Is the legend readable?
- [ ] Are labels, units and time frames included where needed?
- [ ] Is the projection suitable for the purpose?
- [ ] Is the colour scale appropriate for the data type?
- [ ] Are class breaks or value ranges consistent where comparisons are made?
- [ ] Is the layout readable?
- [ ] Are uncertainties or limitations mentioned where relevant?

For satellite RGB or false-colour images, explain the assigned channels, for example:

```text
R, G, B = near-infrared, red, green
```

---

## Captions and interpretation

A figure should not stand alone without explanation.

A useful caption usually states:

- what is shown,
- where and when it is shown,
- which data or method produced it,
- what the reader should notice,
- any key limitation needed for interpretation.

For report writing, figure captions are usually placed below the figure. Table captions are usually placed above the table.

A concise caption pattern is:

```text
Figure X. [What is shown] for [area/time period] based on [data/method]. [Main pattern or interpretation]. [Limitation if needed].
```

Example:

```text
Figure X. Predicted built-up areas in the study area based on a semantic segmentation workflow applied to Sentinel-2 imagery. The largest predicted built-up clusters occur near the main transport corridor. Small isolated predictions should be interpreted carefully because no independent validation labels were available.
```

---

## Visual hierarchy and storytelling

Visual hierarchy means that the most important information is easiest to see.

Use hierarchy through:

- title and caption,
- colour emphasis,
- line weight,
- spacing,
- grouping,
- annotation,
- figure order.

Storytelling does not mean exaggeration. It means guiding the reader from question to evidence to interpretation.

For your final presentation, one strong key-result figure is often more effective than many weak figures.

---

## Potential pitfalls

| Pitfall | Better approach |
| --- | --- |
| Too many figures | Select the few figures that support the main argument. |
| Unclear legend | Simplify classes and use readable labels. |
| Unreadable labels | Increase font size and reduce clutter. |
| No spatial context | Add study area outline, reference locations or a small overview map. |
| Misleading colour scale | Use colour choices that match the data type and avoid false precision. |
| Comparing maps with different class breaks | Use consistent breaks when the goal is comparison. |
| Showing model output without limitations | Add evaluation, uncertainty or a clear caution. |
| Figure not mentioned in the text | Refer to every figure and explain why it matters. |
| Caption only describes colours | Explain the result, not only the visual elements. |

---

## Mini task

Choose one figure or map that could become central to your SDS320 project.

Write:

```text
Figure purpose:
Research question connection:
Data shown:
Method or processing step:
Main message:
Important limitation:
```

Then draft a two-sentence caption.

---

## Key takeaways

- Figures and maps should support your project argument.
- Start every visual with a question and audience.
- Keep maps readable, honest and focused.
- Captions should explain what the figure demonstrates and what it does not show.
- A few strong visuals are better than many unfocused ones.
