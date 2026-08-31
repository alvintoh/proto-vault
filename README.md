# proto-vault

Obsidian vault + git repo for the PRD and ideation phase — every product/app
idea that's been considered, prototyped, or shipped. This repo holds only
docs and prototype links, never application code, plus one project-scoped
Claude Code skill (`/prd-create`, in `.claude/skills/`) that writes here.

## What lives here

- One markdown note per idea/PRD, written by `/prd-create`.
  - At the vault root (flat, one-off ideas): filename `YYYY-MM-DD-<slug>.md`.
  - Inside a project folder (see Structure below): concise names instead —
    the folder already carries the project identity, so a date/slug prefix
    is redundant. E.g. `thor-mart/prd.md`, `thor-mart/architecture.svg`.
- A `## Prototype` section in each note linking out to its Figma file
  (personal draft, so no file-count cap).
- Nothing else — no code, no build tooling. When an idea graduates to
  implementation, the actual app gets its own dedicated repo; this vault
  keeps the note and the prototype link as the historical record.

## Versioning

Once a note or diagram is revised after being drafted, files take an
incremental `-vN` suffix instead of being overwritten in place (e.g.
`prd-v1.md`, then `prd-v2.md`), so prior versions stay as history. The
note's own frontmatter carries a matching `version:` field. This applies
within project folders; a flat one-off note at the vault root doesn't need
it unless it's substantially revised after being finalized.

## Status vocabulary

Each note's frontmatter carries a `status`, so the vault stays a browsable
catalog via Obsidian's own search/tags rather than a folder hierarchy:

| status | meaning |
|---|---|
| `draft` | PRD written, not yet prototyped or ticketed |
| `prototyped` | Figma screens exist, still pre-ticket |
| `ticketed` | Broken into epics/tickets in Linear |
| `shipped` | The app/feature exists and is live |
| `abandoned` | Considered, not pursued — kept for reference |

## Structure

Flat by default — tag and status-filter instead of nesting, so a new idea
never has to fit an existing folder taxonomy.

**Exception**: once an idea has a codename and multiple related artifacts
(PRD note, diagrams, prototype links), it gets its own folder named after
the codename (e.g. `thor-mart/`) so those files stay together. A
single one-off idea with just a PRD note stays flat at the vault root.
