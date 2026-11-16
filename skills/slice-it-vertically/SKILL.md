---
name: slice-it-vertically
description: Breaks an existing spec or plan from the conversation into vertical end-to-end phases saved under docs/phases/. Use after create-spec, or whenever the user wants a plan or spec split into phases, slices, or work packages, or mentions "slice-it-vertically". NOT for small single-slice tickets (use plan-ticket), and NOT for writing the spec itself (use create-spec first).
---

Cut the spec or plan into vertical slices. Do NOT interview — synthesize from spec + codebase.

## Input

In priority order: (a) the spec the user names; (b) the newest matching spec in docs/specs/; (c) the plan in the current conversation. Explore the codebase if you haven't. If nothing specifies the work, stop and say so.

## What counts as a slice — the vertical test

Every slice must pass all three before its file may be written:
1. It has a user-triggerable input and an observable output — if you can't demo it, it's not a slice.
2. Its result is visibly or behaviorally different from the previous slice.
3. It can be built and verified without any later slice landing first.

Banned as phases: Setup, Infrastruktur, Datenmodell, API-Layer, Frontend, Testing, Refactoring, Polish. That work lives INSIDE the slice that first needs it. A test proving a behavior belongs in the slice that ships the behavior — a test-only phase means the previous phase wasn't actually done.

Sanctioned exceptions (use sparingly and justify inside the file):
- **Walking skeleton** as Phase 1 on greenfield: the thinnest real end-to-end path through all major components — hardcoded values and a single happy path are fine. It absorbs the unavoidable scaffolding; architecture and functionality then grow in parallel.
- **Spike**: a time-boxed research slice when an unknown blocks slicing; its acceptance criteria are the questions to answer.
- **Decision gate**: a genuine external HITL prerequisite (contract, approval, another team). ONE decision per gate, placed directly before the first slice that needs it — never a mega-blocker pooling all decisions upfront.

## How to cut

1. Find the core complexity of the spec. List the variations: paths (payment methods, channels, event types), business rules, data variants, interfaces, entities.
2. Reduce every variation to ONE → that is the skeleton / first slice.
3. Add one variation back per slice (SPIDR: Spike, Path, Interface, Data, Rules). Order: skeleton first, then the riskiest/most uncertain slice, then the rest by value.
4. Prefer the split whose later slices could be deprioritized or dropped entirely — if every slice is "mandatory", the cut probably hides waste.
5. Right-size: one slice ≈ one focused agent session, ~1–5 files. More than ~8 files or ~7 acceptance criteria → split again. A small ticket yields 2–4 slices; don't inflate the count to look thorough.
6. Under uncertainty (open questions, novel integrations): fully specify only the skeleton plus the next 2–3 slices; list the remainder as "Candidate slices (re-slice after Phase N)" in the last file's Notes instead of writing fake-precise phase files.

## HITL or AFK

AFK only if ALL hold: every AC machine-verifiable · no credentials or external approvals needed · no visual/UX judgment calls · nothing irreversible. Otherwise HITL. An AFK slice with one embedded human checkpoint is "AFK — HITL gate: {what}". Every Type carries a one-line "weil ..." rationale — misclassification must be visible.

## Process

1. Cut the slices per the rules above.
2. Coverage check: map every spec AC / user story to a slice; name anything unmapped instead of dropping it silently.
3. DAG check: every Blocked-By target exists, no cycles, numbering follows dependency order, no gaps.
4. Present the slice list (one line each: title · Type · how-to-demo) and confirm with the user before writing files. Push back yourself if a slice grew too big — the confirmation step is also a size gate. If the user is not available, record the proposed list plus the assumptions you proceeded on, and continue.
5. Write the files.
6. If slicing resolved a spec Open Question, update the spec too (strikethrough + pointer to the phase). Do NOT modify or close any parent issue beyond that.

Files: `docs/phases/phase-{N}-{short-title}.md`. If docs/phases/ already holds phases of ANOTHER spec, write to `docs/phases/{spec-slug}/phase-{N}-{short-title}.md` instead and say so explicitly (spec-to-jira / phases-to-multica must then be pointed at the subfolder).

## Phase file template

Headings stay English (tools depend on them); content in the user's language.

```
# Phase {N}: {Short Title}
> Spec: {path} ({AC-IDs covered}) · Size: {S|M}, ~{n} files

## Description
End-to-end behavior of this slice — what exists after it that didn't exist before.

## Type
HITL | AFK | AFK — HITL gate: {what} — weil {one line}

## How to demo
One sentence: what a stakeholder can see or do after this phase.

## Acceptance Criteria
3–7 checkboxes, each an observable behavior (WHEN/THEN or "run {command}, expect {result}") — never an implementation step ("create service class" is banned). Concrete file paths as anchors are fine.
- [ ] ...

## Verification
The exact commands / manual steps that prove the acceptance criteria.

## Blocked By
- Phase {N} — {title}: {why it blocks}    (or: None — can start immediately.)
- External: {ticket/team}: {what must land}
- Soft after Phase {M}: {sequencing preference — not a hard blocker}
- Parallel-safe with Phase {M}


## Notes
Risks, edge cases, spec Open Questions resolved here (→ written back to the spec), deferred items, candidate slices.
```

## Terminal state & lifecycle

End by listing the phases (title · Type · demo line) and offering /spec-to-jira or /phases-to-multica. During implementation the phase files are living documents: tick ACs as they pass, and when a phase completes or diverges from plan, add a one-line Status note at the top and reconcile the spec (Status header, Open Questions).
