---
site:
  outline_maxdepth: 2
---

# Report writing

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Writing a concise project report that explains what you did and what you found
</div>
<!-- markdownlint-enable MD033 -->

---

## Purpose of the report

The SDS320 report documents your project in a concise and structured way.

It should explain:

- motivation,
- research question,
- data,
- methods,
- implementation,
- main results,
- challenges,
- limitations.

The report is not a diary of everything you tried. It is a clear account of the project you implemented and what can be learned from it.

The report must include a link to your public GitHub or GitLab repository.

---

## Report constraints

The project report and repository contribute 30% to the final grade.

The report must be submitted as a PDF via the corresponding MS Teams assignment. The deadline is **Monday, 14 December 2026, at 17:00**.

The report should not exceed **6500 characters including spaces**, excluding figures and references.

Do not invent extra requirements beyond the syllabus. Follow updated course instructions if they are announced through the official course channels.

<!-- TODO: add final submission link or Teams assignment name once available. -->

---

## Suggested report structure

Use the structure below as a recommendation. Adapt it if your project needs a slightly different flow.

### 1. Title

Use a clear and specific title. A good title usually conveys the project message or spatial focus, not only the method.

### 2. Motivation and research question

Briefly explain why the project matters and state the research question clearly.

Keep background short. The reader should quickly understand the problem, area and purpose.

### 3. Data

Describe the datasets you used. Include relevant information such as provider, spatial coverage, time period, format, resolution, licence and any important limitations.

### 4. Methods and implementation

Explain the main workflow and method choices. Focus on what is needed to understand and reproduce the analysis.

You do not need to include every code detail. Detailed code belongs in the repository.

### 5. Results

Present the main results using clear text and key figures or maps.

Lead with important results, not with the amount of effort involved.

### 6. Discussion and limitations

Explain what the results mean, how reliable they are and what limitations remain.

Limitations can concern data quality, spatial resolution, model performance, evaluation, assumptions, computing constraints or scope.

### 7. Conclusion

Briefly answer the research question and state the main takeaway.

### 8. References

Include references for data, methods, code sources, literature and other external materials where relevant.

### 9. AI use statement, if applicable

If you used AI tools, describe when and how they supported your work. See [AI use and integrity](../01_course/05_ai-integrity.md).

---

## Writing style

Good scientific writing is clear, concise and reader-friendly.

Use these principles:

- keep it simple,
- assume the reader is scientifically literate but not an expert in your exact project,
- keep only what is essential,
- tell a clear project story,
- write short sentences,
- make one main point per paragraph,
- be precise and consistent,
- use the first sentence of a paragraph to signal the paragraph’s purpose,
- avoid unnecessary jargon,
- explain abbreviations on first use.

Use past tense for what you did and observed. Use present tense for general facts or established knowledge.

```{tip}
Read your report aloud. If a sentence is hard to read aloud, it is probably hard to understand.
```

---

## Writing about methods

The methods section should explain why and how the workflow was implemented.

A useful pattern is:

```text
Input data
→ preprocessing
→ analytical method
→ evaluation
→ output generation
```

Mention software only where it matters for reproducibility. The repository should contain more detailed setup information.

For methods, put the reason before the technical detail where possible:

```text
To compare land-cover patterns across time, the images were first reprojected and clipped to the same study area.
```

This is usually clearer than starting with tool names or low-level operations.

---

## Writing about results

Results should prioritise what matters for the research question.

Avoid “statspeak” or output descriptions that the reader cannot interpret. Explain what a value, map or metric means for the project.

For each key result, ask:

- What did I find?
- Where is it visible?
- How strong or uncertain is it?
- How does the figure support the statement?
- Does this answer the research question?

---

## What to leave out

Because the report is short, leave out:

- long generic textbook explanations,
- every failed attempt,
- every small code detail,
- screenshots of code,
- long package installation notes,
- raw terminal output,
- unnecessary background,
- figures that do not support the argument,
- unsupported claims.

Failed attempts can be mentioned briefly if they explain an important project decision or limitation.

---

## Common pitfalls

| Pitfall | Better approach |
| --- | --- |
| The research question appears only at the end | State it early and return to it in the conclusion. |
| The report describes tools but not decisions | Explain why each key step was needed. |
| Data sources are vague | Name provider, coverage, time period and important limitations. |
| Figures are shown but not interpreted | Explain what each figure demonstrates. |
| The discussion hides problems | State limitations clearly and professionally. |
| The report repeats generic course content | Focus on your project. |
| The repository link is missing | Include the public repository link in the report. |
| AI use is not disclosed | Add a short, accurate AI-use statement if applicable. |

---

## Mini task: five-sentence outline

Draft a five-sentence report outline:

```text
1. This project investigates ...
2. It uses ...
3. The workflow ...
4. The main result shows ...
5. The main limitation is ...
```

Then check whether the five sentences already form a coherent project story.

---

## Key takeaways

- The report should explain what you did, why you did it and what you found.
- Keep the report concise and focused on the project.
- Use figures and maps as evidence, not decoration.
- Discuss limitations honestly.
- Include the public repository link and AI-use statement where applicable.
