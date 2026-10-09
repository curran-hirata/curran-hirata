# Prompt log

A running record of AI sessions that mattered. The assistant appends one entry at the end of every session that changed a file (see AGENTS.md). Never backfilled; past entries are never edited.

## 2026-10-09 — Portfolio repo setup
- **What I asked:** Walk me through building the portfolio repo from the Student Onboarding and Stage 0 pages, then compare the result with the course's setup prompt and bring it in line.
- **What it produced:** The repo skeleton with a stub README in each folder, a first-pass `AGENTS.md`, `CLAUDE.md`, `prompt-log.md`, `.gitignore`, and then revised versions of `AGENTS.md`, `CLAUDE.md` and `prompt-log.md`, plus `analysis/figures/`.
- **What was wrong:** The first-pass `AGENTS.md` was a generic template with blanks, the `CLAUDE.md` and standing-rule wording differed from the setup prompt, this log lacked a "what you produced" field, and `analysis/figures/` was missing. The AI also could not copy the course's Naming section word for word, so I have to paste it myself.
- **How I caught it:** I compared the generated files against the setup prompt on the course page and asked the AI to list the differences. I still need to read every line before committing.
