# /arch-plan — parked design

Parked 2026-09-02. Not yet built; no SKILL.md, so it cannot be invoked.

## Position in the chain

`/prd-create` (business: what the product must contain)
  → `/arch-plan` (ONCE per product: which layers and libs exist)
    → `/spec-plan` (per feature, per ticket, grounded in the repo)

Per-feature architecture stays in `/spec-plan` — it is tracker-bound (ticket key
in, comment out) and grounds against an existing codebase. `/arch-plan` runs
before a codebase exists.

## Decided

- **Cadence: once per product, greenfield.** No brownfield mode.
- **`/prd-create` stays business-level.** What the product must contain —
  problem, users, outcomes, scope. No stack, no libs. Separating the product
  document from the technical one is established practice (PRD vs design
  doc/RFC/ADR): different audiences, and the problem statement outlives several
  rewrites of the architecture.
- **Output sections are NOT invented.** Use `## Frontend` / `## Backend` /
  `## Infra` (+ `## General`) exactly as `poly-mind/base/skills/shared/
  build-intake.md:33-34` defines them, so output flows into `/dev-build` →
  `/agent-mode` with no translation. Builders are already mapped at :53-55.
- **AI features in the product are a `## Backend` concern**, not a peer section.
  Settle the rung there (model call → agentic workflow → agent) and the eval
  requirement that follows, per base/CLAUDE.md.
- **Depth: down to lib-per-layer.** Not "use a query library" but which one,
  and why this product's constraints select it.
- **Consumes `poly-mind/base/docs/engineering-defaults.md`; debates only
  DELTAS.** Re-arguing recorded defaults every run is waste and invites drift.

## Must fix when built

`project-init`'s description contains "asks which libs or tools to use for a
new build" — that clause will steal every `/arch-plan` invocation. It has to
move, not be duplicated (sharpen, don't accumulate).

## Open

- What happens to `/prd-create` step 3 (`:122-136`, rough architecture SVG)?
  Either it stays a deliberately rough sketch and `/arch-plan` supersedes it,
  or step 3 is removed and `/arch-plan` owns the diagram outright.
- Does `/arch-plan` write into the project folder (`<project>/architecture.md`)
  beside `prd-vN.md`, following the vault's -vN convention?

## Acceptance test

Run it against `urban-rest-web`'s client stack. It should independently report
that layer 3 is missing — GraphQL + graphql-request + hand-rolled
`useEffect → await → setState`, with no async-state manager. If it does not
find that, it is not ready.
