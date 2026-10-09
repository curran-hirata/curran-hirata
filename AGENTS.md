# AGENTS.md — AI conventions for this repository

## Who I am
Curran Hirata. <!-- 2–3 lines from your resume: background, focus, what you're building toward. -->

## How I like to work
- I draft first; AI reviews. AI never writes my bio, briefs, analyses, memos, or reflections.
- Give line-level critique, not rewrites, unless I ask for a rewrite.
- Be direct. Flag anything you are unsure about instead of guessing.
<!-- Add your own preferences: tone, level of detail, formatting. -->

## Repository structure
- `docs/briefs/` — scope and hypothesis, before the work
- `docs/decisions/` — dated decision memos (what, why, who, what would reverse it)
- `capabilities/<slug>/spec.md` — what a build must do, written before the build
- `analysis/` — findings and charts
- `data/` — sourced inputs, with provenance
- `models/` — Performance Ratios only
- Organize by capability and engagement, never by course, term, or week.

## Never paste or commit
- Credentials, API keys, `.env` files
- Personal data: <!-- add yours: student ID, address, phone, etc. -->
- Licensed or proprietary material I don't have the right to publish

## Standing rules
- At the end of every session, append an entry to `prompt-log.md`: what I asked, what the AI got wrong, how I caught it. Never backfill.
- Read `docs/decisions/` before proposing an approach; don't re-propose anything already rejected there.
- Every directory must contain at least one file (Git does not track empty folders).
- Commit messages must say what changed (not "update" or "fix").
