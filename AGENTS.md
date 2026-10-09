# AI conventions

## About this repository
Curran Hirata's public portfolio repository for undergraduate-level business coursework: briefs, specs, analyses and decision memos. Canonical file: AGENTS.md. CLAUDE.md points here.

About me: BBA candidate in Accounting at the University of Hawaiʻi at Mānoa, Shidler College of Business (expected May 2027), and Student Internal Auditor at the University of Hawaiʻi Office of Internal Audit. My field is accounting and internal audit: operations, financial systems, compliance and governance review.

## Where things are
- capabilities/<capability>/  a capability, with its spec and model
- docs/briefs/          written BEFORE work: scope + hypothesis
- docs/decisions/       written AFTER work: recommendations
- analysis/             findings and figures (charts go in analysis/figures/)
- data/                 sourced inputs, with provenance
- models/               Performance Ratios only

## Naming
- The directory matters most. A file in the wrong folder is harder to find. If you are not certain which folder a file belongs in, ask me before you write it — do not choose for me.
- Graded files use the exact filename the stage brief gives — lowercase, hyphens, no spaces. Dated documents are YYYY-MM-DD-slug-type.md (no name — the repo is yours); the stage page says so when they do.
- Slugs name the engagement, never the week, the course, or the assignment number.
- Never invent a path or a filename. I will give you the exact one.

## How I work
- Explain concepts fully and walk the worked example. Do not hand me conclusions.
- Critique my reasoning directly. I would rather be corrected than agreed with.
- When you are uncertain, say so and say what would resolve it.
- I know financial and managerial accounting, income tax and accounting information systems. Build on those, and explain software, data and modeling concepts from the ground up. Use audit and accounting terms where they fit.

## What you may and may not draft
- You MAY explain, critique, debug, quiz me, and draft mechanical files.
- You MAY NOT write my briefs, analyses, memos, or reflections.
- Every statistic or figure you give me is a draft until I verify it against a source.

## Documentation
When work changes, update the document that describes it in the same commit. A capability's README names the engagements that exercised it — keep that current.

## Scope
Do the work I asked for. If you notice something worth doing that I did not ask for, tell me instead of doing it.

## Commits
Descriptive messages: what changed and why. Never "update" or "stuff".

## Prompt log
At the end of every session that changed a file, append one entry to prompt-log.md: the date, what I asked, what you produced, what was wrong and how it was caught. Never backfill earlier sessions and never edit a past entry.

## Never include
No credentials, no API keys, no personal data about anyone, no licensed or copyrighted material. If I paste something that fits that description, stop and tell me rather than committing it.

For me, that specifically includes:
- Anything from my work at the University of Hawaiʻi Office of Internal Audit: audit work papers, findings and draft reports, interview notes, risk assessments, documents shared with the external audit team, and anything marked confidential or internal
- University, state or government records: financial system extracts, ledgers, payroll, procurement or asset data, and student or employee records
- Names, contact details or identifying information about colleagues, auditees, classmates or other people
- My own personal details: home address, phone number, student ID

If a document would not be safe in a public repository, it is not safe to paste into a chat window either.

## Mistakes to avoid (append to this list)
Record errors here as they happen, so the same one does not repeat.
- (empty — add the first one when it happens)
