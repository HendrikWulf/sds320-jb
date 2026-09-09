---
site:
  outline_maxdepth: 2
---

# Q&A sessions

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Asking useful questions and answering them with clarity
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why the Q&A matters

The {abbr}`Q&A (Questions and Answers)` session is part of the scientific discussion. For a presenter, it is often what people remember best: a well-rehearsed talk can still leave a strong impression, and a talk with rough edges can be rescued by a confident, thoughtful discussion afterward. How you handle questions says as much about your understanding of your own project as the slides did, sometimes more, since a question can ask you something your slides never had to answer directly.

For the audience, asking a good question is one of the most efficient ways to learn: it sharpens your own thinking, helps the whole room understand a point better, and gives the presenter useful feedback they cannot get any other way. In SDS320, this cuts both ways over the semester. At the concept presentation, your classmates' questions are some of the most useful feedback you will get before you have invested weeks of work. At the final presentation, the questions you ask each other are a direct way of practicing the same critical evaluation skills you need for your own project.

---

## 2. Asking questions

Most people hesitate before raising a hand. The reasons are familiar: the question feels too obvious, or too complicated to phrase well, or you worry you missed something everyone else already understood, or you simply do not want to be the center of attention for a moment.

None of these reasons hold up well in practice. If a question occurred to you, it likely occurred to someone else in the room too, and asking it usually helps more people than just you. The skill of asking a clear, useful question improves specifically through practice, in low-stakes settings like a classroom presentation. And the benefits are concrete: you get the information you actually wanted, you practice thinking critically about someone else's workflow, and you show your classmates and instructors that you are engaged with the material.

```{tip}
If you are unsure whether your question is "good enough" to ask, that hesitation is usually a sign to ask it anyway. The question that feels too basic is often the one that clarifies something for half the room.
```

---

## 3. Active listening

Good questions come from active listening. While you watch a classmate's presentation, it helps to keep a few questions in mind:

- What is this project's core question, and does the data presented actually support the conclusion drawn from it?
- What is the most important piece of evidence here, and does it look solid?
- Is there a detail the presenter moved past quickly that you would like to understand better?
- Does anything here connect to your own project, or to a different classmate's project?
- If you were doing this project, what would you want to try next?

Taking brief notes during a presentation, even just a word or two per question, makes it far easier to ask something specific afterward instead of a vague "can you say more about that?"

A useful question often has three parts:

```text
1. Frame or topic
        ↓
2. Motivation or issue
        ↓
3. Question
```

For example:

> **Frame:** You showed that the model performed well in the validation area.  
> **Motivation:** I was wondering how sensitive that result is to the spatial split.  
> **Question:** Did you test whether performance changes when the validation area is farther away from the training data?

This structure helps the presenter understand both **what you are asking** and **why you are asking it**.

---

## 4. Types of questions

Most questions after a project presentation fall into a handful of recognizable types. Knowing them helps you both formulate your own questions and recognize what a questioner is actually asking you.

- **Clarification** — "I did not quite follow how you got from the raw imagery to that mask. Could you walk through that step again?" You are asking to understand something you missed, not challenging it.
- **Elaboration** — "Did you see the same pattern in the other tiles, or just this one?" You want more detail on a result already shown.
- **Connection** — "Does this relate to the water-mapping example from the segmentation lesson, or the change-detection workflow?" You are linking the project to something else you know.
- **Challenge** — "How confident are you that this improvement is not just from the smaller test set?" You are pushing on an assumption, a method choice, or an interpretation.
- **Suggestion in disguise** — "You could try comparing this against a simpler baseline." This is really advice, not a question; the presenter's best move is usually to acknowledge it as worth trying.
- **Opinion** — "What do you think explains the outlier here?" The questioner wants your reasoning, not a fact you can look up.
- **Tangent** — "This is not really about your project, but does this technique work on video too?" Interesting, but outside the project's main scope.

```{admonition} A useful shape for a question
:class: tip
Many clear questions follow the same pattern: **frame** (which part of the presentation you mean), **issue** (what specifically raised your question), then **question** (what you actually want to know). "In the evaluation section, your per-class results varied a lot between classes. What do you think is driving that difference?" is easier to answer than "What about the results?"
```

---

## 5. Answering questions

Presenting is a one-way delivery of information you have rehearsed. Answering questions is different: you do not know what is coming, and the audience is watching how you handle that uncertainty as closely as they watched your slides.

A few principles apply across almost every question:

- **Listen to the whole question before responding.** It is easy to start formulating an answer halfway through and miss the actual point being raised.
- **Paraphrase if you are not sure you understood.** "So you are asking whether the model would generalize to a different city?" costs a few seconds and prevents answering the wrong question entirely.
- **Give a direct answer first, then explain.** For a question with a clear yes, no, or short answer, lead with it before adding detail. This respects the audience's attention and makes your reasoning easier to follow.
- **Stay calm, even for a pointed question.** A defensive or irritated response reads as weaker than a calm one, even if the question was genuinely difficult.

---

## 6. Practical answering tips

```{tip}
When you don't understand a question, say so and ask for clarification, rather than guessing and answering something else. A confidently wrong answer is worse than a short pause to ask "could you rephrase that?"
```

```{tip}
When you need a moment to think, take it. "That's a good question, let me think about that for a second" buys you real time and reads as thoughtful, not unprepared.
```

```{tip}
When a question is technical and part of the audience might not follow it, add brief background before answering directly, so your response is useful to the whole room, not just the questioner.
```

```{tip}
When you do not know the answer, say so plainly. "I don't know, but here is what I do know that's related" is far more convincing than an improvised guess, and it is one of the most respected answers you can give.
```

```{tip}
When you sense the real motivation behind a question, address it directly. If someone is really asking "does this generalize beyond your one test area," answering that question is more useful than answering only the literal words used.
```

```{tip}
When you have evidence that supports your point, add it briefly. A short reference to a related result, a comparison, or a check you already ran strengthens your answer more than restating your conclusion.
```

```{tip}
When a question deserves more time than the room has, say so and offer to follow up. "That needs a longer answer than we have time for, let's talk after" is a legitimate and common response, not a way of dodging the question.
```

---

## 7. Handling tricky situations

Some situations come up often enough that it helps to have a plan ready.

**Nobody asks anything.** This can happen for several reasons: the talk ran long, the topic felt outside people's interest, or the audience assumes everyone else already understands. You can prompt discussion yourself: mention a question you expected and answer it, or ask the audience a specific question about a result you found genuinely uncertain.

**A question turns into a monologue.** If someone is sharing their own related work at length rather than asking something, it is fine to acknowledge the connection warmly and move to the next question. You do not need to let it run indefinitely.

**Someone disagrees strongly.** State your position calmly once, note that you have limited time for questions, and offer to continue the conversation afterward. You do not need to resolve every disagreement in front of the room.

**You are asked about something you deliberately left out of the presentation.** It is fine to explain briefly why you scoped it out, rather than launching into everything you cut for time.

---

## 8. Things to avoid

- **Answering a different question than the one asked.** If a question is inconvenient, resist the urge to redirect to a more comfortable topic; it is usually obvious to the audience.
- **Turning an answer into a second presentation.** A long, unstructured response that dumps in everything you left out of your slides is harder to follow than a short, direct one.
- **Passing responsibility elsewhere.** Explaining a limitation is fine; blaming a tool, a dataset, or someone else for it reads as evasive.
- **Getting defensive.** An irritated or dismissive tone undermines an otherwise solid presentation faster than almost anything else.
- **Bluffing.** Guessing at an answer and presenting it with confidence is riskier than admitting you do not know; it tends to come apart under a follow-up question.

---

## 9. Flags & checks

| Red flag | First check |
| --- | --- |
| You do not understand the question | Ask for clarification, or paraphrase it back to confirm |
| You need time to think | Say so directly; a short pause is fine |
| You do not know the answer | Say so honestly, and share what you do know that is related |
| The question goes beyond your project's scope | Acknowledge it and offer to follow up afterward |
| Part of the audience looks lost during a technical question | Add brief background before answering |
| A question is really a comment or suggestion | Acknowledge it as worth considering, and move on |
| You feel yourself getting defensive | Pause, stay calm, and focus on the data rather than the tone |
| Nobody asks a question | Prompt with a question you anticipated, or ask the audience one directly |

---

## 10. Mini task

Before your next presentation, do both halves of this task.

**As the presenter:** write down two or three questions you expect to be asked, and draft a short, direct answer for each, including at least one question you are not fully sure how to answer yet.

**As the audience:** pick one classmate's project you know is coming up, and prepare one clarification question and one connection or challenge question you could ask, using the frame-issue-question shape from earlier on this page.

---

## 11. Key takeaways

- The Q&A often shapes how a presentation is remembered.
- Asking questions is a skill that improves with practice, and most hesitation to ask is not justified by the actual risk involved.
- Recognizing the type of question being asked, clarification, challenge, suggestion, or opinion, helps you answer it well.
- A calm, direct answer, including an honest "I don't know" when true, is more convincing than an improvised or defensive one.
- Practicing anticipated questions and answers beforehand is as valuable as rehearsing the presentation itself.
