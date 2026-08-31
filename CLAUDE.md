# proto-vault — Claude Code notes

This repo holds PRD/ideation content (see `README.md`) plus one Claude Code
skill, `.claude/skills/prd-create/SKILL.md` — kept here rather than in
`poly-mind` (the separate config repo for skills/agents/rules) because this
PRD flow is used almost entirely with proto-vault as the working directory
anyway, so co-locating the skill with the notes it writes avoids a
cross-repo pointer for no real benefit.

- **Project-scoped, not global**: `/prd-create` only triggers when the
  working directory is inside this repo. It won't be picked up from an
  unrelated project the way a poly-mind-projected skill would.
- **poly-mind stays config-only**: its own `CLAUDE.md` requires it remain
  business/product-agnostic — PRD content and prototype links don't belong
  there, and (as of 2026-08-30) neither does this skill.
- **poly-mind location** (for other, unrelated personal/work skills):
  `C:\development\personal\private\poly-mind`.
