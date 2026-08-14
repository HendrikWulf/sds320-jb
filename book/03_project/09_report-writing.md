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

---

## Suggested report structure

Use this as a recommendation and adapt it where needed.

| Section | Function |
| --- | --- |
| Title | Name the project clearly and specifically. |
| Motivation and research question | Explain why the project matters and state the question early. |
| Data | Describe datasets, sources, coverage, time period, format and limitations. |
| Methods and implementation | Explain the workflow and key method choices. |
| Results | Present the main outputs with key figures or maps. |
| Discussion and limitations | Explain meaning, reliability, uncertainty and limitations. |
| Conclusion | Briefly answer the research question. |
| References | Cite data, methods, literature, code sources and external material. |
| AI use statement, if applicable | State when and how AI tools supported your work. |

For AI guidance, see [AI use and integrity](../01_course/05_ai-integrity.md).

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

## Writing about methods and results

For methods, explain why and how the workflow was implemented.

```text
Input data
→ preprocessing
→ analytical method
→ evaluation
→ output generation
```

Mention software where it matters for reproducibility. Detailed code belongs in the repository, not in the short report.

For results, lead with what matters for the research question. For each key result, ask:

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

## Flags & checks

| Red flag | First check |
| --- | --- |
| The research question appears late | State it near the beginning. |
| The report describes tools but not decisions | Explain why each key step was needed. |
| Data sources are vague | Name provider, coverage, time period and limitations. |
| Figures are shown but not interpreted | Explain what each figure demonstrates. |
| The discussion hides problems | State limitations clearly and professionally. |
| The report repeats generic course content | Focus on your project. |
| The repository link is missing | Add the public repository link. |
| AI use is not disclosed | Add a short, accurate AI-use statement if applicable. |

---

## Mini task

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
- Use figures and maps as evidence.
- Discuss limitations honestly.
- Include the public repository link and AI-use statement where applicable.
