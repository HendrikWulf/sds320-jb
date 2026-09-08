---
site:
  outline_maxdepth: 2
---

# Effective feedback

<!-- markdownlint-disable MD033-->
<div class="page-subtitle">
Making peer discussions genuinely useful, for you and for others
</div>
<!-- markdownlint-enable MD033 -->

---

## 1. Why feedback matters

SDS320 relies on peer discussion at several points: after your concept presentation, after your final presentation, and often informally when a classmate asks what you think of their repository, their figures, or a section of their report. In every one of these moments, you are either giving feedback or receiving it, and the quality of that exchange has a direct effect on how much the course actually helps your project improve.

This page treats feedback as a skill with its own techniques, the same way the [Q&A page](11_questions-and-answers.md) treated asking and answering questions as a skill. Being a good listener or a nice person is not the same as giving feedback someone can actually act on, and being confident is not the same as receiving feedback in a way that lets you use it.

---

## 2. Feedback is not evaluation

It helps to keep two things separate. Evaluation judges a finished result against a standard, a grade, a pass/fail decision, a ranking. Feedback is different: it is a description of what someone observes in your work, offered while there is still time to do something about it.

Peer feedback in this course is almost always the second kind. Your classmates are not grading your project when they comment on your concept presentation; they are telling you what stood out, what confused them, and what they would want to know more about, so you can act on it before your final submission. Treating a peer's comment as a verdict on your work, rather than as one useful data point, is a common way to either dismiss it too quickly or take it too personally.

---

## 3. Giving effective feedback

A few habits separate feedback someone can act on from feedback that just sounds nice or sounds harsh.

- **Be specific.** "The methods section was hard to follow" is a start; "I lost track of why you chose Sentinel-2 over Landsat around the third paragraph" is something the presenter can actually fix.
- **Focus on the work, not the person.** Describe what you saw in the project, the workflow, the figure, the argument, rather than what you think about the person who made it. "This map's legend is hard to read" lands very differently from "you didn't think about your audience."
- **Say what you can support with a reason.** "I would try a different color scale" is more useful when paired with why: "because the current one makes it hard to distinguish the middle values, which is where most of your data falls."
- **Balance what is working with what could improve.** Naming a genuine strength is not padding; it tells the presenter what to keep doing, which matters as much as knowing what to change. This does not mean forcing a compliment before and after every critical comment; an unbalanced mix in either direction is less useful than an honest one.
- **Focus on what the person can still change.** A comment on something fixed and unchangeable (the size of the study area they already committed to, for instance) is frustrating rather than useful; a comment on something they can still act on before the next milestone is worth far more.
- **Keep it to a few points.** Three specific, well-explained comments are more useful than ten quick ones. If everything comes at once, none of it gets prioritized.

```{admonition} A structure worth borrowing: Situation, Behavior, Impact
:class: tip
The **Situation-Behavior-Impact** framework, developed by the Center for Creative Leadership, is normally taught for workplace feedback, but it adapts cleanly to project feedback: name the specific place in the work you mean (**situation**), describe exactly what you observed there (**behavior**), then explain why it matters (**impact**). For example: "In your validation section (situation), you split training and validation points randomly across the whole study area (behavior), which risks overestimating accuracy because nearby points tend to be similar (impact)." This is more useful than "your validation seems off," because it gives the presenter something concrete to check.
```

For a longer discussion, a useful sequence is:

```text
Clarify (Ask questions before giving advice)
   ↓
Value (Identify what is already strong)
   ↓
Raise concerns (Explain what remains uncertain or problematic)
   ↓
Suggest (Offer one or more possible next steps)
```

The important order is that you **understand first** and **advise later**.

---

## 4. Receiving feedback well

Receiving feedback is its own skill, and it is easy to do badly even with good intentions.

- **Listen all the way through before responding.** It is tempting to start explaining or defending a decision the moment you sense where a comment is going, but that often means you never actually hear the full point.
- **Ask for clarification rather than guessing.** "Can you say more about which part felt unclear?" gets you something you can use; assuming you know what someone meant risks fixing the wrong thing.
- **Separate the comment from your reaction to it.** A comment on your project is not a comment on your ability. Notice if you are getting defensive, and treat that feeling as a signal to listen more carefully, not less.
- **Decide what to do with it; you do not have to agree with everything.** Not all feedback is equally well-founded. Weighing a comment and deciding not to act on it is a legitimate outcome, as long as you actually considered it rather than dismissing it on reflex.
- **Write it down.** Feedback given in a five-minute discussion after a presentation is easy to forget by the time you are back at your laptop. A short note taken on the spot is often the difference between feedback that changes your project and feedback that evaporates.

```{tip}
If you get feedback that stings a little, that is often a sign it touched on something real. It is worth sitting with an uncomfortable comment for a day before deciding it was wrong.
```

---

## 5. Where this comes up in SDS320

Peer feedback in this course is not limited to one moment; the same underlying skills apply in slightly different forms:

- **After the concept presentation** — feedback here is mostly forward-looking: is the question well-scoped, is the planned data realistic, what risks does the audience see that you might not.
- **After the final presentation** — feedback here often engages more directly with results: does the evidence support the conclusion, what would the audience want to see validated further.
- **On a repository** — when a classmate looks at your code or your README, useful feedback focuses on whether someone unfamiliar with the project could understand and run it, not on stylistic preferences alone.
- **On a report draft** — feedback on writing works best when it addresses the big-picture structure and argument first (does the analysis support the conclusion, is the flow logical) before smaller wording issues, since a paragraph that gets restructured makes line-level comments on it moot anyway.

The specific comments differ across these four situations, but the same core habits from this page (be specific, focus on the work, listen fully, ask before assuming) apply to all of them.

---

## 6. Written feedback needs a bit more care

Spoken feedback lets you read a reaction and adjust immediately. Written feedback, comments on a document, a pull request, a shared slide deck, does not, so a few things matter more than they do out loud:

- Attach a comment to the specific place it applies to (a line, a slide, a paragraph), rather than a general note at the end that leaves the reader guessing what you meant.
- Read a written comment back before sending it. Tone is easy to misjudge in text, and a comment that would sound constructive spoken aloud can read as blunt or dismissive on a screen.
- If you have several comments, a short one-sentence summary at the top ("overall this is clear; my main comment is about the validation split") helps the reader prioritize before they read every individual note.

---

## 7. Things to avoid

- **Vague praise or vague criticism.** "Nice job" and "this needs work" both leave the other person with nothing to act on.
- **Editing someone else's work for them.** Pointing out that a paragraph is hard to follow is feedback; rewriting the paragraph yourself usually is not what was asked for, and it takes the decision away from the person whose project it is.
- **Piling on every possible issue at once.** More comments are not automatically more helpful; an overwhelmed recipient often acts on none of them.
- **Framing feedback as a verdict.** "This part doesn't work" is harder to use than "this part was confusing to me because...", since the second version tells the presenter what to actually address.
- **Dismissing feedback because it was awkwardly delivered.** A blunt or clumsy comment can still contain something worth considering; it is worth separating the delivery from the content.

---

## 8. Flags & checks

| Red flag | First check |
| --- | --- |
| Your comment is a general reaction ("this felt off") | Add the specific place and reason it felt that way |
| You are only listing problems | Add at least one thing that is genuinely working well |
| You are commenting on something the presenter cannot change now | Focus on what is still actionable before the next milestone |
| You feel defensive while receiving a comment | Pause, listen fully, and ask a clarifying question before responding |
| You disagree with a piece of feedback | Consider it seriously before deciding not to act on it, rather than dismissing it immediately |
| Written comments feel harsh when you reread them | Soften the wording, but keep the substance |
| You have ten comments to give | Narrow to the two or three that matter most |

---

## 9. Mini task

Before your next peer discussion, prepare both sides.

**As a reviewer:** pick one classmate's project you will see presented, and prepare one specific, actionable comment using the situation → observation → reason structure from earlier on this page.

**As the presenter:** after you receive feedback (from a peer or an instructor), write down, in your own words, what the comment actually meant and one concrete thing you will do in response. If you decide not to act on a piece of feedback, write down why.

---

## 10. Key takeaways

- Feedback describes what someone observed, while there is still time to act on it; it is not the same as a grade or a verdict.
- Specific, work-focused, actionable comments help far more than general praise or general criticism.
- Receiving feedback well means listening fully, separating the comment from your reaction to it, and deciding deliberately what to do with it.
- The same core habits apply whether the feedback is about a presentation, a repository, or a report draft; only the specific focus changes.
- A few well-explained comments are more useful than many quick ones, in either direction.

---

## 11. Further reading

- University of Waterloo, Centre for Teaching Excellence, ["Receiving and Giving Effective Feedback"](https://uwaterloo.ca/centre-for-teaching-excellence/catalogs/tip-sheets/receiving-and-giving-effective-feedback) — a concise tip sheet covering both sides of the exchange, and the main source behind the general principles on this page.
- Center for Creative Leadership, ["The SBI Feedback Model"](https://www.ccl.org/articles/leading-effectively-articles/sbi-feedback-model-a-quick-win-to-improve-talent-conversations-development/) — the original source for the Situation-Behavior-Impact structure introduced above.
- Google, ["How to Do a Code Review"](https://google.github.io/eng-practices/review/reviewer/) — official guidance on reviewing someone else's code constructively, directly relevant when you look at a classmate's repository.
- George Mason University Writing Center, ["Providing Feedback to Writers"](https://writingcenter.gmu.edu/writing-resources/writing-as-process/providing-feedback-to-writers) — practical guidance for giving useful feedback on a report or draft, including how to prioritize big-picture issues over line-level wording.
- Monash University, [Learn from feedback](https://www.monash.edu/student-academic-success/understand-assessments/learn-from-feedback)  
  Student-oriented guidance on using peer and instructor feedback critically and turning it into improvements.
