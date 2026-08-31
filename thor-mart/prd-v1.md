---
title: Thor-Mart — Electricity Plan Comparison (Phase 1 of a Multi-Utility Comparison Platform)
date: 2026-08-31
tags: [prd, thor-mart, electricity, utilities, nbn, mobile, comparison-platform, australia]
status: draft
version: 1
---

## Summary
A public platform that lets Australian residential and small-business users compare electricity retail plans against their own usage and get back a ranked list of options — not a single "best" pick — starting with electricity as the proof-of-concept category ahead of utilities, NBN, and mobile. Long-term this is intended to grow into a public product covering all four categories, with the codebase itself open in a public repo.

## Problem
People comparing electricity plans in Australia today have no single place that shows utilities, NBN, and mobile together, and even within electricity, existing comparison sites are frequently commercially biased toward sponsored retailers rather than surfacing the objectively best-value plan. Comparison is also treated as a one-off event — nobody is watching whether a better deal appears after the fact — and even after someone finds a better plan, actually switching provider is a separate, disconnected process from the comparison itself.

## Goals & success metrics
Success here is deliberately a *set* of measurable signals rather than one KPI, since a single number can't capture whether the tool is actually working:
1. **Value delivered** — of users who enter their current plan, the percentage whose top-ranked recommended plan(s) beat it, and by how much (baseline to be established post-launch; no target number yet — see Open Questions).
2. **Adoption/usage** — number of comparisons run per month, and return-usage rate.
3. **Downstream intent** — click-through rate from a listed plan to the retailer's signup page (a proxy for value, since v1 does not execute the switch itself).

## Users & personas
- **Residential household** — enters home usage (e.g. annual kWh or a recent bill) and location, gets ranked household electricity plans.
- **Small business** — same flow, but with business tariff inputs (e.g. demand charges, contracted rates), which differ enough from residential that they need distinct handling, not a shared assumption.

Both personas are user-selectable at the start of the comparison flow — the product does not assume one audience.

## Requirements

### Functional
1. Given a user starting a comparison, When they select their user type (residential or small business) and enter location (postcode/state) and usage details (annual kWh or a recent bill amount), Then the system returns a ranked **list** of matching electricity plans ordered by estimated annual cost — never collapsed to a single recommendation.
2. Given a returned list of plans, When the user views a plan, Then its card discloses retailer, plan name, estimated annual cost, key tariff terms, and a link to the retailer's own signup page (switching itself stays external/manual in v1).
3. Given pricing data may originate from more than one source (open government data and/or retailer-sourced data), When a plan is displayed, Then its data source and last-updated date are shown, so a user can judge freshness and trust.
4. Given the product will not cover 100% of the AU electricity retail market in v1, When a user views comparison results, Then a visible disclosure states coverage is partial (exact wording pending the compliance research in Open Questions).
5. Given the ambition for ongoing monitoring (tentative, best-effort for v1 — see Open Questions), When a better-value plan than the user's current one becomes available after their initial comparison, Then a user who opted in is notified — this requirement may slip to a later phase if infeasible within v1's data-refresh approach.
6. Given AI-based ranking is applied on top of the deterministic cost-sort in Functional #1, When its quality is evaluated against a held-out labelled set of household profiles with a known best plan, Then the match-rate threshold for "good enough to ship" is set once that labelled set and a baseline exist (see Open Questions) — the deterministic cost-sort remains the guaranteed ranking at all times, so AI ranking can never make results worse than v1's baseline.
7. Given a user asks a natural-language question about their own plan (RAG, grounded in that plan's actual terms), When the AI is not confident in its answer, Then the answer is still shown but visibly flagged as low-confidence, rather than blocked or hidden.
8. Given plan data is populated via AI-assisted extraction from retailer sites, When a field's extraction confidence is low, Then that field is flagged for manual review and held back from publishing until reviewed, and the expected review load must fit within roughly 1 hour/week given a solo builder (see Open Questions if actual volume exceeds this).

### Non-functional
- Pricing data is refreshed periodically (not true real-time) in v1, sourced from a combination of open government data where available and retailer-sourced data to fill gaps.
- Every displayed plan carries a visible data-source label and refresh timestamp (supports Functional #3).
- Core comparison (residential and small business) is usable without requiring an account; only opt-in monitoring/alerts may require one.
- Since the repository is intended to go public, no secrets, credentials, or user-submitted usage data may be hardcoded or logged insecurely anywhere in the codebase.
- For users opted into monitoring/alerts (Functional #5), notifications should be delivered promptly after a qualifying data refresh rather than waiting for the next scheduled batch. The exact latency target and delivery mechanism (e.g. an event-driven pipeline) is a technical decision for `/spec-plan`, not fixed here — this PRD only states that promptness matters for subscribed users.

## Scope

### In scope
- AU electricity retail plan comparison only.
- Both residential and small-business personas, user-selectable.
- Results always returned as a ranked list, never a single answer.
- Periodic (non-real-time) price data, sourced from a mix of open government data and retailer-sourced data.
- Best-effort: opt-in "better deal appeared" monitoring/alerting (may slip to a later phase — see Open Questions).

### Out of scope
- Gas/water utilities, NBN, and mobile comparison — deferred to later phases of the broader platform vision.
- Assisted or completed provider switching — v1 only links out to the retailer; it doesn't execute the switch.
- True real-time/live pricing lookups for any category in v1.
- Large commercial/industrial tariffs — small business only, not enterprise.
- Any claim of full/complete market coverage.

## Constraints & dependencies
- No fixed deadline — side-project pace.
- Data source for AU electricity pricing is not yet finalized: likely a mix of government open data (e.g. Energy Made Easy/AER) and direct retailer sourcing — needs investigation before build starts.
- Repository is intended to be public — any data source or license chosen must be compatible with an open codebase.
- Solo builder/owner — all scope and priority decisions currently rest with one person.
- Manual review capacity for AI-flagged items (Functional #8) is roughly 1 hour/week — any AI-assisted feature that would exceed this on a regular basis needs redesign, not just tighter thresholds.
- Technical preference noted (non-binding): an event-driven pattern for the alerts pipeline, to support prompt delivery and independent scaling — to be evaluated in `/spec-plan` against the real codebase, not decided here.

## Risks & open questions
- **Regulatory/compliance**: AU energy comparators other than the government's own Energy Made Easy generally must disclose limited market coverage — the exact disclosure/compliance requirements are unresearched and must be confirmed before public launch. (Supporting evidence: under the National Energy Customer Framework, retailers are legally required to give Energy Made Easy data on every plan, while commercial comparators like Finder/Canstar/iSelect/Compare the Market are not required to show the full market and earn referral fees on switches — see [CHOICE's write-up](https://www.choice.com.au/shopping/shopping-for-services/utilities/articles/energy-comparison-sites).)
- **Data source feasibility**: whether a usable, sufficiently-licensed open dataset exists, versus needing to scrape or partner directly with retailers — unresolved, and affects both cost and legal exposure.
- **Success metric targets**: the specific numeric thresholds for Goal #1 (e.g. "X% find a cheaper plan") can't be meaningfully set before a usage baseline exists — to be defined post-launch/beta.
- **Monitoring/alerting scope**: whether Functional #5 makes it into v1 or slips to a later phase depends on data-refresh feasibility discovered during build.
- **Monetization**: long-term business model (affiliate commissions, subscription, or free) is explicitly undecided for v1.
- **Real-time data for future categories**: which categories/providers eventually warrant true live lookups vs. periodic refresh is unresolved — a future-phase question, not a v1 blocker.
- **AI ranking eval set doesn't exist yet**: the labelled household-profile set needed to measure Functional #6 has to be built before any match-rate threshold can be set.
- **RAG grounding source undefined**: which plan documents/fields are actually available to ground natural-language answers (Functional #7) isn't decided yet.
- **AI-assisted scraping review load is unproven**: the ~1 hour/week budget (Functional #8, Constraints) is a guess — if real low-confidence volume is higher, either the extraction approach or the budget needs to change.

## Wishlist / future ideas
Looser, unrefined ideas worth keeping visible — not yet cross-examined to the same bar as Requirements above, and not commitments for any specific phase:
- AI-based plan ranking/matching, RAG-grounded Q&A, and AI-assisted data scraping (captured properly as Functional #6-8 above, but the broader ambition is bigger than what's specified there — e.g. deeper personalization, richer conversational plan advice).
- Event-driven architecture as the general backbone, beyond just the alerts pipeline — for scaling out to gas/water, NBN, and mobile categories.
- An AI workflow/agent that goes beyond ranking — e.g. proactively negotiating, drafting a switch request, or managing renewals — well beyond v1's "comparison only" scope.
- Expansion to gas/water, NBN, and mobile (already named as later phases in Scope) plus assisted/completed switching (also already named as out of scope for v1).

## Stakeholders
- Owner/approver: Alvin Toh — sole decision-maker on scope and priorities for this project.

## Architecture (rough, illustrative)

Logical components only — stack and infra decisions are made in `/spec-plan`, not here.

![[architecture-v1.svg]]
