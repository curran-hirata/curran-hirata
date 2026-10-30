# Prompt log

A running record of AI sessions that mattered. The assistant appends one entry at the end of every session that changed a file (see AGENTS.md). Never backfilled; past entries are never edited.

## 2026-10-09 — Portfolio repo setup
- **What I asked:** Walk me through building the portfolio repo from the Student Onboarding and Stage 0 pages, then compare the result with the course's setup prompt and bring it in line.
- **What it produced:** The repo skeleton with a stub README in each folder, a first-pass `AGENTS.md`, `CLAUDE.md`, `prompt-log.md`, `.gitignore`, and then revised versions of `AGENTS.md`, `CLAUDE.md` and `prompt-log.md`, plus `analysis/figures/`.
- **What was wrong:** The first-pass `AGENTS.md` was a generic template with blanks, the `CLAUDE.md` and standing-rule wording differed from the setup prompt, this log lacked a "what you produced" field, and `analysis/figures/` was missing. The AI also could not copy the course's Naming section word for word, so I have to paste it myself.
- **How I caught it:** I compared the generated files against the setup prompt on the course page and asked the AI to list the differences. I still need to read every line before committing.

## 2026-10-29 — Stage 1 executive brief (online-degrees)
- **What I asked:** Explain the Stage 1 assignment, then help me produce the executive brief for Scenario 16 (EUR 3,000,000 receivable, 273 days) and check that the capability README is in place, using the Stage 1 page, the deliverable-templates page and `template-decision-memo.md`.
- **What it produced:** Several drafts of `docs/briefs/2026-10-29-online-degrees-hedge-brief.md`, revised to paragraph form, impersonal wording and the course's section structure. A starter `capabilities/fx-hedging/README.md`. A review of my own edits to the brief. I edited and committed the files myself, because the AI could not push to the repo.
- **What was wrong:** The AI drafted the brief before reading `AGENTS.md`, which says it may not write my briefs. Early drafts used a table and bullets, "we/our" wording, and placeholder interest rates and a spot rate it could not verify. One of its tests for being wrong, about the put's break-even, could never fail, because the premium is fixed. It also could not read the template or my repo at first, so it guessed at the template's headings.
- **How I caught it:** I compared the drafts with the Stage 1 checklist and the template, asked the AI to review my edits, and found the `AGENTS.md` rule once the repo was attached. I still need to verify every figure against the givens and rewrite the parts that are not my own words.
