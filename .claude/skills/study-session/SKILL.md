---
name: study-session
description: Run a structured tutoring session on a system design (or any STEM) concept in this repo. Use when the user wants to study, learn, revise, or prepare for an interview/exam on a topic, says they're confused or keep forgetting something, or asks to be quizzed. Prefer this over just explaining, since a plain explanation is exactly what fails a learner who struggles to retain material.
---

# Study Session

For a student who struggles to understand STEM concepts and tends to forget them. The flow:

1. **Topic** — the student names a topic.
2. **Lesson** — write the in-depth lesson file. Hand it over.
3. **Discussion** — the student reads it and drives what happens next: questions, or a quiz, back and forth, until they've actually got it.
4. **Save** — once they've got it, write the quick notes to the repo, push flashcards to study-bot, push a session note to study-bot.

Don't rush to Save just to close out the session — that's the last step, not a deadline.

## Rules

- Never advance to Save while the student is still confused. Slow beats broad.
- The student's own retrieval effort builds memory — when they ask "do I have this right?", have them explain it back rather than just confirming.
- One concept cluster per session.
- Warm, never condescending — normalize struggle, celebrate the click.
- Move at the speed of their answers. A sharp, correct answer gets banked, not re-asked "just to be sure."
- **A lesson is done when it gives full intuition, not when every section is filled in.** Cut sections that don't earn their place for this specific topic. Don't pad for the sake of looking complete — a short lesson that's actually clear beats a long one covering every angle.
- Use as many diagrams as actually clarify the mechanism — zero, one, or several. One diagram trying to show structure *and* request flow *and* failure modes is worse than three small ones, each showing one thing.

## Big topics

If the topic naturally splits into several sub-concepts each substantial enough for their own lesson (e.g. "Scaling from 0 to millions" → single server, load balancer, cache, ...), run Steps 1–3 below **once per subtopic**, in teaching order, each in its own subfolder — don't cram them all into one `lesson.md`.

- `<topic_path>` = `topics/<NN>_<Topic>`, the numbered folder convention owned by the `system-design-notes` skill (highest existing number + 1, zero-padded, e.g. `topics/01_Network_Protocols/`) — reuse it, don't invent a different one.
- Subtopic path: `<topic_path>/<MM>_<Subtopic>/` (`<MM>` = `01`, `02`, ... in teaching order), same `lesson.md` + `README.md` + `diagrams/` layout as any topic.
- `<topic_path>/README.md` gets written once (after the last subtopic, or updated as you go) — a short summary of the overall arc, linking to each subtopic's `README.md`. No `<topic_path>/lesson.md` — depth lives in the subtopics.
- Below, `<path>` means the subtopic path (`<topic_path>/<MM>_<Subtopic>`) for a big topic, or plain `<topic_path>` for a topic with no subtopics.

## Step 1 — Lesson

Write `<path>/lesson.md`.

Pull from whichever of these actually build intuition for *this* topic — skip what doesn't:

- **Problem** — the real need, plain language.
- **Core idea** — analogy first, then the precise mechanism, mapped back onto the analogy.
- **Trade-offs** — what's given up, what's gained.
- **Where it's used** — 1-2 real systems.
- **Worked example(s)** — narrated step by step, why each step happens, not just what.
- **Diagram(s)** — see Diagrams below.

This file is what gets handed to the student — don't write it and then explain something different live.

## Step 2 — Discussion

The student reads the lesson, then either asks questions or asks for a quiz. Go back and forth — answer, quiz, re-explain — using whichever tool fits what's actually needed:

- **Direct Q&A** — answer tying back to the lesson's analogy/mechanism, not a fresh explanation from scratch.
- **Quiz** — 5–8 questions, mostly MCQ (4 options, real-misconception distractors), max 1–2 free-recall. Use a tappable-options tool if available. Batch questions, reveal all answers with a one-line "why" after.
- **Feynman check** — ask them to explain a piece back in their own words, as if teaching a friend. Vague spots = thin understanding — go fix those, don't move on.
- **You-do-one / predict-the-next-step** — for a worked-example-heavy topic, give a fresh variant and have them solve or narrate it.

Keep going until they can do a Feynman check or a you-do-one unaided. That's the signal to move to Save — not a fixed number of rounds.

## Step 3 — Save

Once they've genuinely got it:

1. **Notes** — invoke the `system-design-notes` skill to write/update `<path>/README.md` (terse quick-reference, separate from the in-depth lesson).
2. **Flashcards** — 5–12 atomic cards (one fact/concept each — split comparisons into one card per item). Rules:
   - Question: specific, unambiguous, ≤300 chars
   - Answer: direct and complete on its own, ≤500 chars
   - Prefer "apply it" over "define it" — test recall, not recognition
   - No walls of text — if the answer needs more than 2 sentences, it's two cards
   - Lists/steps: bullet format `• point one\n• point two` (literal `\n` for line breaks in Telegram)

   Show as a table, then push via `add_cards_bulk` on the study-bot MCP. Confirm how many were added. If the MCP isn't connected this session, say so and fall back to just the table.
3. **Session note** — push via `add_session_note` on the study-bot MCP, content in exactly this format (links use the GitHub URL format from Close below):

   ```
   # Study Pack: [Topic]

   ## What we covered
   [2-3 sentences]

   ## Lesson
   [GitHub link to <path>/lesson.md]

   ## Notes
   [GitHub link to <path>/README.md]
   ```
4. **Spaced review schedule** — actual dates. Default: Day 0 → 2 → 5 → 12 → 26 → 45. Compress for a deadline (exam in a week: today, +1, +3, +6; exam tomorrow: today/tonight/morning-of, and say plainly this trades retention for a short-term boost). Each review = recall the flashcards + resolve one problem, not passive re-reading. Give this in chat — no separate push needed.

## Diagrams

Whenever a diagram would clarify part of the lesson, use the `d2-diagram` skill to write the D2 source, then render and embed it — never leave a diagram as unrendered source. There can be more than one; give each a descriptive name rather than numbering them generically:

1. Save source to `<path>/diagrams/<descriptive-name>.d2`.
2. Render: `d2 <file>.d2 <file>.svg`, where `<file>` is that same path without extension.
3. Embed in the markdown: `![<Label>](diagrams/<descriptive-name>.svg)`

Re-render step 2 any time a `.d2` source changes.

## Close

Commit `lesson.md` and `README.md` (ask before pushing, per normal git rules). Hand over both GitHub links plus the next concrete action ("next review is [date], ~10 min"). Encouraging, concrete.

Links are `<remote>/blob/<branch>/<path>/lesson.md` and `.../README.md` (drop the `.git` from the remote URL, e.g. `https://github.com/<owner>/<repo>/blob/main/<path>/lesson.md`) — read the remote and branch from `git remote get-url origin` and `git branch --show-current` rather than guessing. They only resolve once pushed; say so if they aren't yet.

## Output template

End-of-session chat reply: the same block pushed as the session note (Step 3 above), plus two sections it doesn't carry:

```
# Study Pack: [Topic]

## What we covered
[2-3 sentences]

## Lesson
[GitHub link to <path>/lesson.md]

## Notes
[GitHub link to <path>/README.md]

## Flashcards
[Confirmation of how many were pushed via add_cards_bulk, or "MCP not connected" fallback]

## Review schedule
- [Date] — today (done)
- [Date] — ...
```
