---
name: system-design-notes
description: Add or review a system design topic in this repo (e.g. "add notes on rate limiting", "add a topic for consistent hashing", "review my CAP theorem notes"). Keeps every topic in the same folder structure so the repo stays a usable reference instead of scattered notes.
---

# System Design Notes

This repo is a from-scratch system design learning log. One numbered folder per topic.

## Topic folders

Each topic is `topics/<NN>_<Title_With_Underscores>/`, e.g. `topics/01_Network_Protocols/`. `<NN>` is a zero-padded 2-digit sequence number: list existing `topics/*` folders, take the highest `NN`, add 1 (start at `01` if `topics/` has none yet). Never renumber existing folders when adding a new one.

Inside that folder:
- `README.md` — quick notes (this skill)
- `lesson.md` — in-depth lesson (the `study-session` skill)
- `diagrams/<name>.d2` / `.svg` — as many as needed, shared by both

## Adding a topic

Create `topics/<NN>_<Name>/README.md` with these sections:

1. **Problem** — what real need this solves, 2-3 sentences.
2. **Core idea** — the mechanism, in plain language.
3. **Trade-offs** — what you give up, what you gain.
4. **Where it's used** — 1-2 real systems.
5. **Diagram(s)** — zero, one, or several D2 diagrams, whatever actually clarifies the flow.

Keep entries short — a working mental model, not a textbook chapter. Don't pad a section out just to fill it in; skip what doesn't earn its place for this topic.

## Diagrams

When a diagram would help, use the `d2-diagram` skill to write the D2 source, then render and embed it. There can be more than one — one per distinct thing worth visualizing (structure, request flow, failure mode), not one diagram trying to show everything:

1. Save source to `topics/<NN>_<Name>/diagrams/<descriptive-name>.d2`.
2. Render: `d2 <path>.d2 <path>.svg`
3. Embed in the README: `![<Label>](diagrams/<descriptive-name>.svg)`

Re-render (step 2) every time a `.d2` source changes, so the embedded SVG never goes stale.

## Reviewing a topic

Read the existing `topics/<NN>_<Name>/README.md`, check it against the 5 sections above, and flag gaps or outdated trade-offs rather than rewriting from scratch.

## Index

After adding a topic, add one line to `topics/README.md` (create it if missing): `- [Name](topic-folder/README.md) — one-line hook`.
