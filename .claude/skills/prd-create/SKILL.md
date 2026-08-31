---
name: prd-create
description: Turn a raw business requirement into a complete PRD — cross-examine until goals, users, scope and constraints are settled rather than assumed, draft it, show it for approval, then push to Obsidian and prompt to break it into epics/tickets in Linear. Use for "create a PRD", "draft a PRD for X", "turn this idea into a product requirements doc", "spec out this initiative". Upstream of /ticket-feat and /spec-plan.
argument-hint: '[the business requirement or idea]'
---

Turn a business-requirement idea into a PRD whose scope and acceptance bar are
settled *before* the document is written, not inferred afterward. `$ARGUMENTS`
may describe the idea; if it came up earlier in this conversation, draft the
interrogation from that context — don't re-ask what's already known.

**Draft first, push only on approval — same inviolable gate as `/ticket-feat`
and `/ticket-bug`:** never write to Obsidian and never create anything in
Linear until the user explicitly picks that option. The user may only want the
PRD text.

## Flow
cross-examine the idea (gated, iterative) → draft the full PRD → **show it,
gate: approve / edit** → **rough architecture diagram (gated)** →
**prototype the UI in Figma (gated)** → **push to Obsidian (gated)** →
**always prompt: break into epics/tickets for Linear? (gated)** → draft
epics/tickets → **gate: file to Linear / draft only**.

## 1 — Cross-examine (the step this skill exists for)

Don't draft from the first description — interrogate it. Ask enough follow-up
questions, in batches via `AskUserQuestion` where they're genuinely
either/or, in prose where they need an open answer, to settle:

- **Problem** — who has it, what they can't do today, why it matters now. A
  restated solution ("we need a dashboard") is not a problem statement —
  press until you have the underlying gap.
- **Goals & success metrics** — what does "this worked" look like, measurably.
  Reject a goal you can't later check against a real number or observable
  outcome.
- **Whether any requirement is NON-DETERMINISTIC** — if the answer depends on a
  model reading free-form input rather than rules someone could write down, its
  acceptance criterion is not pass/fail: it needs a **success threshold**, the
  **labelled set** it is measured against, and **what happens below threshold**
  (a classifier at 85% means 15% go somewhere — decide where, or they go nowhere
  silently). Only the requirement owner can set those, which is why they belong
  here. Also settle the **human-intervention budget**: how often a person may
  have to step in before the feature is not worth having. **Do not pick a
  technology** — the rung and the stack are `/spec-plan`'s job.
- **Users / personas** — who is this for; if there's more than one audience,
  name each and whether their needs actually differ.
- **Scope boundaries** — what's explicitly in, what's explicitly out. An
  unscoped PRD is the single most common way this document becomes useless
  three weeks later.
- **Constraints** — deadlines, systems that must not change, compliance/legal
  boundaries, budget or headcount limits, known dependencies on other teams
  or in-flight work.
- **Stakeholders** — who owns the final call on scope, who signs off, who
  needs to be looped in before this goes to Linear.
- **Risks & open questions** — anything genuinely unresolved. Write it down
  as an open question in the PRD rather than inventing an answer — a labelled
  gap is trustworthy; a confident guess becomes spec fiction that flows
  downstream into every ticket cut from it.

**Keep cross-examining until these are actually answered, not until you've
asked once.** If an answer is vague ("make it fast", "for enterprise users"),
push once more for the specific, checkable version before moving to the
draft. Two or three rounds is normal for anything non-trivial.

## 2 — Draft the PRD

```
# <PRD title — the outcome, not the feature name>

## Summary
<2-3 sentences: what this is, who it's for, why now.>

## Problem
<the gap, who has it, why it matters. No solution yet.>

## Goals & success metrics
<what "this worked" looks like, in measurable/observable terms.
SMART fits here — it is what Doran defined it for. Per-requirement quality bars
belong on the requirement, not here; this section carries the business outcome.>

## Users & personas
<who this serves; note if needs diverge across audiences.>

## Requirements
### Functional
<numbered; each an observable behaviour, not an implementation detail.
**Given-When-Then** for the criterion itself — the established BDD format, and it
carries context, action and observable consequence in one line. A NON-DETERMINISTIC
requirement fits it too, and must name all three parts: "Given a held-out set of
human-labelled alerts, When the classifier runs, Then >=85% match the human label,
and anything below threshold routes to manual triage." The bar is not just
measurable but FALSIFIABLE — it must be able to fail for its own reason (mutation
testing's principle, and the same rule base CLAUDE.md holds tests to). "95%
endpoint uptime" is perfectly measurable and says nothing about correctness.>
### Non-functional
<performance, security, compliance, scale constraints — WHAT must hold, not
WHICH stack/infra delivers it; see the note below.>

## Scope
### In scope
<what this PRD covers.>
### Out of scope
<named exclusions — "none" only if genuinely none.>

## Constraints & dependencies
<deadlines, systems that can't change, other teams/tickets this depends on.>

## Risks & open questions
<what's genuinely unresolved; who needs to settle it. Omit if empty.>

## Stakeholders
<owner, approvers, who to loop in.>
```

Show the **full drafted PRD as the body of the message**, then — same turn,
immediately below it — gate via `AskUserQuestion`:
- **Approve as-is (Recommended)** — proceed to the push step.
- **Edit first** — apply the requested changes, re-show, re-gate.

Never proceed past this gate on assumption; only on an explicit approve.

## 3 — Rough architecture diagram (gated, optional but default-yes)

Before the high-fidelity Figma prototype, sketch the system's shape — cheaper
to revise a rough diagram than a mockup (the same cost-of-change logic as
step 4, one rung earlier: diagram → mockup → code, each stage costing more
to change than the last). This is illustrative only — **no stack or infra
decisions**, same rule as the rest of this skill (see Notes).

Gate via `AskUserQuestion`:
- **Generate the diagram (Recommended)** — proceed below.
- **Skip — go straight to Figma** — go to step 4.

On approval, write a hand-authored SVG (not Mermaid) as `architecture-vN.svg`
in the project folder, embedded in the PRD note under a `## Architecture
(rough, illustrative)` section via `![[architecture-vN.svg]]`. Mermaid was
tried first and rejected: Obsidian renders it natively, but its default
theme reads flat/generic — a custom SVG is still a plain static file
Obsidian embeds natively (and stays plain-text/XML editable), while giving
full control over the "editorial" look below. House style, established
2026-08-31 and expected to hold across PRDs unless the user asks to deviate:

- **Off-white canvas** (`#fafafa`) with a subtle repeating dot-grid pattern
  background.
- **Shape = role**: rounded rectangle for the root/entry app; a plain
  rectangle (small `rx`, e.g. 8) for a service/process; a cylinder for an
  actual database; a parallelogram for an external data source; a hexagon
  for an external channel/interface. Never reuse a shape across roles.
- **Color = category**: the root gets its own accent (e.g. orange); each
  functional or type-based group gets a distinct accent reflected in a
  colored, rounded-top header banner (bold white uppercase label) on its
  card, plus matching borders/tag-pills on the boxes inside it — never a
  bare floating text label as a card's only heading.
- **Cards get a soft drop-shadow** (a `feDropShadow` filter, low opacity,
  small blur) and generous rounded corners; inner item boxes stay flat for
  contrast.
- **Connectors are orthogonal only** (right angles, no diagonals/curves).
  Arrowheads go **only on the final segment landing on an actual box** —
  never on a bus/trunk segment, and never into a background card container.
  Dashed, no-arrowhead lines mark same-tier relationships (siblings in one
  group) or brief cross-cutting annotations — never a real data flow.
- **Legend is its own small padded card**, tucked in a free corner, listing
  shape+color meaning — never a bare list competing with the diagram for
  space.
- **Cross-cutting concerns** (CI/CD, monitoring/observability, ChatOps, cloud
  infra) apply to every service, not one — give them their own small
  dedicated panel with its own accent color and header banner (placed near
  the legend, clear of the main flow), holding brief dashed "chip"
  annotations. Never tuck them inside one specific service's card (that
  visually scopes them to just that service) and never draw a connector
  from them to every card (unnecessary — the panel's label already says
  "applies to all services"). A concern genuinely specific to one flow
  (e.g. an auth gate on one particular connector, a token cache beside it)
  stays inline on that connector instead. These are all `/spec-plan`
  decisions; the diagram only acknowledges they exist.
- **No emoji** anywhere in the file, matching the base rule for all files.
- **Padding discipline**: at least ~10-15px between a card's header banner
  and its first child; a database cylinder's body needs enough height that
  its title/subtitle text clears the bottom curve (≥75px body when using a
  14px-radius lid/base ellipse); at least 10px inset between a card's border
  and the boxes inside it.

This is genuinely subjective — expect several rounds. Show the file, ask the
user to open it in Obsidian and react, adjust, re-show. Gate the same way as
step 4: **looks right, continue** / **needs changes** / **a gap surfaced —
loop back to step 1**, per the pattern below.

## 4 — Prototype the UI in Figma (gated, optional but default-yes)

Before pushing the PRD or breaking it into tickets, turn the approved
requirements + personas into screens the user can react to — cheaper to
revise a mockup than a merged PR (the classic cost-of-change curve: a defect
caught at design time costs a fraction of one caught after code exists).

Gate via `AskUserQuestion`:
- **Generate prototype screens (Recommended)** — proceed below.
- **Skip — text PRD is enough** — go straight to step 5 (push to Obsidian).

On approval:
- **Create the file as a personal draft, not inside a team** — personal
  drafts are unlimited on Figma's free tier and don't count against the
  3-file team cap. Only move it into a team file later, once a collaborator
  actually needs to co-edit it.
- **Follow the Figma MCP's own skill chain** — load `/figma-generate-design`
  (which itself requires `/figma-use` first) to translate the Functional
  Requirements + personas into one artboard per key user-facing screen — not
  a stack decision, just what the screen shows and where the primary actions
  sit.
- **Show the resulting Figma file link** and gate:
  - **Looks right — continue (Recommended)** — proceed to step 5.
  - **Refine further** — describe the changes, regenerate, re-show, re-gate.
  - **A screen revealed a requirement gap** — loop back to step 1's
    cross-examination for the affected requirement, redraft the PRD section
    (re-show, re-gate per step 2), then return here.
- **Save a screenshot per screen, both mobile and desktop viewport**, via
  `get_screenshot`, into a `figma/` subfolder of the project folder (e.g.
  `thor-mart/figma/`) — named `<screen-slug>-desktop.png` /
  `<screen-slug>-mobile.png` — so the vault keeps a visual record even if
  the Figma file changes later. The Figma file itself stays on Figma's
  servers; only these exported snapshots live locally.
- **Reference the Figma file URL from the PRD** once step 5 (Obsidian push)
  runs — add it under a `## Prototype` section (linking the saved
  screenshots too) so the note and the visual stay linked, the same way
  tickets carry the Obsidian URI in step 7.

## 5 — Push to Obsidian (gated)

**Do not write the file until the user picks this option.** Offer via
`AskUserQuestion`:
- **Push to Obsidian (Recommended)** — write it now.
- **Skip — draft only** — leave it as chat text; the user pastes it themselves.

On approval:
- **Vault**: this repo (`proto-vault`) — flat structure, no subfolders by
  design, see its `README.md`. Ask again only if the user names a different
  location for this particular note.
- **Write with Obsidian-native conventions**: YAML frontmatter (`title`,
  `date`, `tags`, `status`), the PRD body as-approved, and `[[wikilink]]`
  references for anything that should cross-link to related notes (an epic,
  a related PRD) if the user names one.
  - **`status`** follows the vault's own vocabulary (`draft` → `prototyped`
    → `ticketed` → `shipped` → `abandoned`, per the vault README): write
    `prototyped` here if step 4 produced approved screens, otherwise
    `draft`. Step 7 updates this same field to `ticketed` on filing — never
    introduce a parallel status elsewhere.
- **Filename**: at the vault root, `YYYY-MM-DD-<slug>.md`, matching the date
  the PRD was finalized, not when the idea started. Inside a project folder
  (see the vault README's folder-per-project exception), use a concise name
  instead — `prd.md`, or `prd-vN.md` once the note is revised after being
  finalized, per the vault's versioning convention.
- Confirm the written path back to the user.

## 6 — Always prompt to break into epics/tickets (gated — never skip this ask)

**Whether or not Obsidian was chosen, always ask this next** — a PRD that
never turns into tracked work is dead on arrival, so surface the option every
time rather than waiting to be asked:

- **Break it down now (Recommended)** — proceed to step 7.
- **Not yet** — stop here; the PRD stands alone until the user is ready.

## 7 — Draft epics/tickets, then gate before filing to Linear

- **One epic per major goal or scope boundary** from the PRD — not one epic
  per requirement; group related functional requirements under it.
- **One ticket per independently-shippable requirement**, in the same shape
  `/ticket-feat` uses (problem, user-visible outcome, acceptance criteria as
  observable behaviours, out of scope) — each ticket's acceptance criteria
  should trace back to a specific requirement or success metric in the PRD,
  not restate the whole document.
- **Link every epic and ticket back to the Obsidian PRD note** when step 5
  was taken — the PRD is the source of truth and the ticket is a projection
  of it, not a copy. Include the note's `obsidian://open?vault=<vault>&file=<path>`
  URI (or the vault-relative path if the workspace doesn't use the URI
  scheme) in the epic/ticket description itself, not only in the acceptance
  criteria that trace to it. If step 5 was skipped (draft-only, no Obsidian
  push), there's nothing to link back to — the ticket stands alone.
- **Show the full drafted set — epics and their child tickets — before
  filing anything.** Same inviolable gate as `/ticket-feat`: no Linear
  create-issue call until the user explicitly approves.

Gate via `AskUserQuestion`:
- **File all to Linear (Recommended)** — create the epics, then the tickets
  under them, via the Linear MCP tools; confirm the team/project to file
  into if not already established in this conversation.
- **Edit the breakdown first** — apply changes, re-show, re-gate.
- **Draft only — don't file** — hand over the drafted text; the user files
  it themselves or later.

On filing: report back the created epic(s) and ticket keys/links, and — if
step 5 pushed an Obsidian note — update its frontmatter `status` to
`ticketed` so the note stays current with what actually happened. Never
`@mention` or assign anyone without separate, explicit approval on that exact
recipient — same rule as every other filing skill in this family.

## Notes
- **No stack or infra decisions here** — mirrors `/ticket-feat`'s "no contracts"
  rule (its §4) one level up. A PRD states requirements and constraints (what
  must be true), never which framework/database/hosting delivers them.
  Architecture and infra choices are grounded against the real codebase in
  `/spec-plan`, not guessed at requirements time.
- **This is upstream of `/ticket-feat` and `/spec-plan`, not a replacement.**
  A PRD produces *several* tickets from one initiative; a single feature idea
  that doesn't need a PRD still goes straight to `/ticket-feat`. Once a
  PRD-derived ticket is ready to be planned, `/spec-plan` takes it from there.
- **This skill is project-scoped to `proto-vault`, not global.** It lives at
  `proto-vault/.claude/skills/prd-create/` (this file), not in `poly-mind` —
  poly-mind must stay config-only and business/product-agnostic, and this
  personal PRD flow is primarily used with proto-vault as the working
  directory anyway, so co-locating the skill with the notes it writes avoids
  a cross-repo pointer for no real benefit. The one real cost: this skill
  only triggers when the working directory is inside `proto-vault` — it
  won't be picked up from an unrelated repo the way a poly-mind-projected
  skill would.
- **Linear filing mechanics** (team/project resolution, epic-vs-ticket parent
  linking, label conventions) are drafted above from first principles, not
  copied from an existing shared contract — `ticket-feat`'s shared filing
  contract (`base/skills/shared/ticket.md`, in poly-mind) is
  Jira/Atlassian-MCP-specific; a Linear-equivalent shared contract may be
  worth extracting once this skill and `/ticket-feat`'s Linear path both
  stabilize, rather than each skill hand-rolling its own Linear calls.
- **A PRD edited after its tickets are filed doesn't propagate automatically**
  — the filed epics/tickets are a point-in-time projection of the Obsidian
  note. If the PRD is substantially revised later, re-check whether the
  already-filed tickets still match; this skill doesn't detect or flag drift
  between the two.
- **Figma's free tier caps team files at 3, not personal drafts** — a
  personal draft file is unlimited and uncapped, so step 4 defaults to
  creating there. The tradeoff: personal drafts aren't built for
  collaboration, so the day a second person needs to co-edit, the file moves
  into a team and the cap (or a paid seat) applies again.
