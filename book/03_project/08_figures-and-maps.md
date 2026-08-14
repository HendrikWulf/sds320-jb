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

Figures and maps are evidence for your project argument.

A good figure helps the reader understand what you analysed, what you found and why it matters. A weak figure can confuse the reader, hide uncertainty or make a result look more precise than it is.

Start every visual with a question:

```text
What should the reader learn from this figure?
Where is the result located?
Why does this visual matter?
Who is the audience?
```

---

## What makes a good project figure?

A good figure or map usually:

- supports the research question,
- focuses on one main message,
- has a clear caption,
- shows enough spatial context,
- includes legend, labels, units and time frame where needed,
- uses suitable colour choices,
- is readable in report and presentation formats,
- avoids unnecessary clutter,
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

- [ ] Does the map answer or support a specific question?
- [ ] Is the study area clear?
- [ ] Is the legend readable?
- [ ] Are labels, units and time frames included where needed?
- [ ] Is the projection suitable for the purpose?
- [ ] Is the colour scale appropriate for the data type?
- [ ] Are class breaks consistent when comparing maps?
- [ ] Are uncertainty or limitations mentioned where relevant?

For satellite RGB or false-colour images, explain the assigned channels, for example:

```text
R, G, B = shortwave-infrared, near-infrared, red
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

Visual hierarchy means that the most important information is easiest to see. Use hierarchy through title, caption, colour emphasis, annotation, spacing and figure order.

Storytelling does not mean exaggeration. It means guiding the reader from question to evidence to interpretation.

For your final presentation, one strong key-result figure is often more effective than many weak figures.

---

## Potential pitfalls

| Pitfall | Better approach |
| --- | --- |
| The figure does not support the research question | Remove it or change the message. |
| The map has no spatial context | Add study area outline, reference locations or overview map. |
| Labels or legends are hard to read | Simplify classes and increase readability. |
| Colours imply false precision | Use a colour scale that matches the data type. |
| Maps are compared with different class breaks | Use consistent breaks where comparison is intended. |
| Model output is shown without limitations | Add evaluation, uncertainty or a clear caution. |
| Caption only describes colours | Explain what the figure demonstrates. |

---

## Mini task

Choose one figure or map that could become central to your project.

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
