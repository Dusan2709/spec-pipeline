---
name: create-spec
description: Turns the current conversation context or a completed sync-us capture into a spec saved under docs/specs/. No interview — pure synthesis. Use after a sync-us session or a substantial design discussion, when the user wants a spec or PRD written down. NOT for small tickets or bugfixes (use plan-ticket), and NOT for splitting work into phases (that is slice-it-vertically, which runs after this).
---

Produce a spec from what is already known. Do NOT interview the user. Do NOT invent answers — an unresolved point stays visibly unresolved.

## Process

1. **Resolve the input.** In priority order: (a) the sync-us capture the user names; (b) the newest capture in docs/sync-us/ with Status completed or completed-with-assumptions that matches the topic; (c) the current conversation. If none covers the topic, stop and say what's missing — a spec built on guesses is worse than none.
2. **Read the constraints.** All files in docs/guidelines/ and the relevant docs/adr/ entries, if they exist. The spec must not conflict with them. If guideline files contradict each other, don't silently arbitrate — pick the one that seems current and flag the conflict in Open Questions.
3. **Explore the repo** if you haven't. Sketch the major modules to build or modify — look actively for deep modules: lots of functionality behind a simple, stable, independently testable interface.
4. **Present before writing:** show the module sketch plus a 5-line spec summary. If the user responds, incorporate; if the user is absent, proceed and record the module cut as unconfirmed in Open Questions.
5. **Write the spec** to `docs/specs/{ticket-id-or-feature-slug}.md` using the template below. If the spec originates from a sync-us capture, reuse the capture's slug (minus the date prefix) so the artifacts visibly belong together.
6. **Self-review before saving** — fix or flag, only then write the file:
   - every decision from the capture/context appears; nothing silently dropped, nothing invented
   - no section contradicts another (especially: Solution must not assert what Open Questions still lists as open)
   - every AC verifiable by test or observation, without interpretation — no "funktioniert korrekt"
   - every unresolved point carries an inline `[NEEDS CLARIFICATION: question]` marker AND an Open Questions entry with an owner
   - all flags from the capture arrived in Open Questions
   - length ≤ ~2 pages for a normal feature; much longer → tell the user this probably wants splitting into several specs

## Altitude rule

The spec describes WHAT and WHY at behavior/module level. Two kinds of concreteness differ:
- **Facts about existing code** — verified paths, schemas, existing tests, endpoints — belong in Code References. Implementers need these anchors; blurring hard-won recon into vague prose destroys its value.
- **Prescriptions for new code** — file names to create, function bodies, CSS classes — do NOT belong in a spec. That is the implementer's freedom, and freezing it here goes stale immediately.

## Spec template

Headings stay English (tools depend on them); content in the user's language. Omit a section only if it has nothing real to say — never fill one with boilerplate.

```
# {Title} ({Ticket-ID})
> Status: Draft · Datum: {date} · Quelle: {capture path, "nach sync-us (N Fragen)" | Konversation} · Ticket: {ID or —}

## Problem Statement
The problem from the user's perspective. For existing systems: current behavior → why it is wrong or insufficient.

## Solution
The target behavior from the user's perspective. Assert only what is decided.

## User Stories
Numbered, "As a [role], I want [feature], so that [benefit]", each with priority P1/P2/P3. Only stories that carry real information — no implementation decisions in story costume, no padding to look complete. Skip the section if the ACs say it all.

## Acceptance Criteria
AC-1, AC-2, ... — prefer WHEN/THEN phrasing ("WHEN a return exceeds 50 €, THE SYSTEM SHALL require manager approval"). Each verifiable.

### Edge Cases
Only edge cases that are reachable in this system AND change the implementation. No imaginary corner cases.

## Implementation Decisions
Modules to build/modify, interface changes, schema changes, API contracts. Each nontrivial decision as: **Decision** — what · **Warum** — rationale · **Verworfen** — rejected alternative (one line). The implementer works in a fresh session without this conversation; unexplained decisions get relitigated or ignored. For every NEW topic, event, schema, or API endpoint this spec introduces, list its fields/shape — external contracts are spec content, not implementation detail.

## Code References
Verified facts about the EXISTING codebase, with exact paths: relevant files, schemas, tests, endpoints. (From recon / the capture.)

## Testing Decisions
What makes a good test for this feature, which modules get tested, prior art in the codebase.

## Assumptions
What this spec takes for granted: environment, data, other teams' behavior.

## Out of Scope
Explicitly not part of this spec — each with a one-line reason. Where useful, add boundaries for the implementer: "Ask first: ..." / "Never touch: ...".

## Open Questions
{question} → {owner}. Mirrors every [NEEDS CLARIFICATION] marker. When a later phase resolves one, strike it through here with a pointer to where it was decided.
```

## Terminal state

Present the saved spec path and the Open Questions list, and ask the user to review/accept the spec before recommending /slice-it-vertically — the phase gate between artifacts is where errors are cheapest to fix. The spec stays a living document: update the Status header when implementation lands, and keep Open Questions in sync as they get resolved.
