# AGENTS.md — AI conventions for this repository

This is the canonical conventions file. `CLAUDE.md` points here so any tool finds it.

## About me
Curran Hirata. BBA candidate in Accounting at the University of Hawaiʻi at Mānoa, Shidler College of Business (expected May 2027), and a Student Internal Auditor at the University of Hawaiʻi Office of Internal Audit. My field is accounting and internal audit: operations, financial systems, compliance and governance review.
<!-- Optional: add one line on what you're building toward. It isn't on your resume, so I left it for you. -->

## Where things are
- `capabilities/<slug>/` — one folder per capability, each with `README.md`, `spec.md` and the model file
- `docs/briefs/` — written BEFORE work begins: scope and hypothesis
- `docs/decisions/` — written after the work: dated decision memos (what, why, who, what would reverse it)
- `analysis/` — findings, with charts in `analysis/figures/`
- `data/` — sourced inputs, with provenance
- `models/` — Performance Ratios only
- Organize by capability and engagement, never by course, semester or week.

## Naming
<!-- Paste the Naming section word for word from https://adamwstauffer.github.io/ai-lms/ai-conventions.html -->

## How I work and how I want things explained
- I draft first; AI reviews. AI may explain, critique, debug, quiz me, and draft mechanical files.
- AI may not write my briefs, analyses, memos or reflections.
- Explain accounting, audit and technical concepts fully, but start from what I already know (financial and managerial accounting, income tax, accounting information systems) rather than assuming I know software or data tooling.
- Critique my reasoning directly. Line-level critique, not rewrites, unless I ask for a rewrite.
- State uncertainty plainly, and say what would resolve it. Treat every statistic as a draft until it is verified against its source.
- Do only the work I ask for. Update related docs in the same commit.
- Write commit messages that say what changed (not "update" or "fix").

## Never paste into a model, and never commit
- Credentials, API keys, tokens, `.env` files
- Anything from my work at the University of Hawaiʻi Office of Internal Audit: audit work papers, findings and draft reports, interview notes, risk assessments, documents shared with the external audit team, and anything marked confidential or internal
- University, state or government records: financial system extracts, ledgers, payroll, procurement or asset data, and student or employee records
- Names, contact details or identifying information about colleagues, auditees, classmates or other people
- My personal details: home address, phone number, student ID
- Licensed or copyrighted material I don't have the right to publish (textbook chapters, publisher slides, paid datasets). Cite it instead.

If a document would not be safe in a public repository, it is not safe to paste into a chat window either.

## Standing rules
- At the end of every session that changed a file, append one entry to prompt-log.md: the date, what I asked, what you produced, what was wrong and how it was caught. Never backfill earlier sessions and never edit a past entry.
- Read `docs/decisions/` before proposing an approach; don't re-propose anything already rejected there.
- Every directory must contain at least one file (Git does not track empty folders).
