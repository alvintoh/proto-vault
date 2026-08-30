# proto-vault

Obsidian vault + git repo for the PRD and ideation phase — every product/app
idea that's been considered, prototyped, or shipped. This repo holds only
docs and prototype links, never application code. Skills that write here
(`/prd-create`) live in `poly-mind`, not in this repo.

## What lives here

- One markdown note per idea/PRD, written by `/prd-create`, filename
  `YYYY-MM-DD-<slug>.md`.
- A `## Prototype` section in each note linking out to its Figma file
  (personal draft, so no file-count cap).
- Nothing else — no code, no build tooling. When an idea graduates to
  implementation, the actual app gets its own dedicated repo; this vault
  keeps the note and the prototype link as the historical record.

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

Flat. No subfolders by default — tag and status-filter instead of nesting,
so a new idea never has to fit an existing folder taxonomy.
