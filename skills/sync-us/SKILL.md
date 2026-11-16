---
name: sync-us
description: Interviews the user about a plan, feature, or design — one question at a time, every answer checkpointed to a resumable capture file in docs/sync-us/. Use when a topic needs several design decisions aligned before implementation (roughly a day of work or more), when the user wants to stress-test a plan, resume a paused interview, or says "sync-us" or "schnacken". NOT for small tickets or bugfixes of about one story point — use plan-ticket for those.
effort: xhigh
---

Interview me about this topic until we reach shared understanding, resolving the decision tree branch by branch. The ONLY output of this skill is the capture file — no implementation, no spec, no code. The completed capture is the input for /create-spec.

## Standing rules (hold for the entire interview, even after context compaction)

1. **One question per turn, via AskUserQuestion, always with your recommendation.** Recommended option first, labeled "(Recommended)".
2. **Checkpoint before you continue.** After EVERY answer, update the capture file (Q&A entry + Summary + Open branches) BEFORE asking the next question. If the session dies, unwritten answers are lost — that is the entire reason this skill exists. Never batch writes.
3. **The capture file is the source of truth, not your memory.** After a context compaction, re-read it before doing anything else.
4. **Never ask what you can look up.** Codebase, ticket, docs/specs/, docs/guidelines/ — explore first; ask only genuine judgment calls.
5. **No implementation during the interview** — not even "quick" scaffolding. Deflect "lass uns einfach anfangen" by naming the open branches that would then be built blind.

## Setup (before Q1)

1. Check docs/sync-us/ for a capture file on this topic with Status: in-progress or paused. If found: offer to resume; on resume, re-read it fully, restate the current state in 2–3 sentences, never re-ask answered questions, continue at the "→ NEXT" cursor in Open branches.
2. Recon: explore the codebase, ticket, and context documents relevant to the topic and write the verified facts into the "Context established" section of a fresh capture file at `docs/sync-us/{YYYY-MM-DD}-{topic-slug}.md` (include the ticket ID in the slug if there is one). Non-software topic (rollout, org change, process)? Then there is no codebase — recon the available documents instead, and keep every question in the domain's language (Filialen, Verträge, Wellen — not repos and unit tests).
3. Build the decision tree: list the branches to resolve in "Open branches", dependency-ordered (upstream decisions first). Triage each branch:
   - **Fact** — recon already answers it → record it under Context established, no question.
   - **Default** — any reasonable person would pick the same → collect these and confirm them in ONE batched question ("Diese Defaults nehme ich an — welche willst du ändern?"). One-by-one default confirmation fatigues the user into rubber-stamping.
   - **Judgment call** — real alternatives with real consequences → one-at-a-time interview.
4. Tell me in one line where you're saving. Then ask Q1.

## Question craft

- Funnel per branch: open a fuzzy branch with ONE open framing question ("Wie stellst du dir X vor?"); pin it down with concrete options afterwards. Don't open a branch with multiple-choice when you don't yet know the option space.
- One decision per question — never double-barreled. 2–4 options, neutral and symmetric, each option's description stating its consequence/tradeoff; the recommendation is labeled, never baked into the question wording. No leading forms ("Sollten wir nicht einfach ..."). Don't add an "Other" option — the UI provides free input anyway.
- Ask only about scope, risk, and intent. Inferable details become stated assumptions in the capture file, not questions.
- Evaluator gate: after each answer, judge it sufficient or ambiguous. If ambiguous, ask at most TWO follow-ups quoting the user's words ("Du sagst X — gilt das auch für Y?"), then move on and record the residue as a flag. A vague answer ("so wie üblich") never enters the file as a decision.
- If the user accepts every recommendation without pushback, switch to either/or questions without a marked recommendation for the remaining judgment calls — unanimous acceptance usually means the options decided, not the user.
- When an answer spawns new branches, add them to Open branches and note it on the Q&A entry as "Folge:".
- If an answer contradicts something you verified in recon (or an earlier decision), say so and resolve it now; correct earlier entries when a later answer changes them.
- Question budget: aim for 5–12 substantive questions. At the budget, show the remaining Open branches and ask: continue, defer the rest as flags, or wrap up.
- Acknowledge answers neutrally — no "Super Idee!".
- If the context is getting full, proactively suggest pausing — the capture file makes resuming lossless.

## Pausing and proxy mode

- **Pause** (user leaves or asks to stop): set Status: paused, set the "→ NEXT" cursor on the next open branch, give a one-line recap. Resuming later must cost nothing.
- **Proxy mode** (user delegates: "entscheide den Rest selbst"): allowed — but every self-answered decision is marked **[ASSUMED]** in the Q&A log and collected under "Assumed pending confirmation". Finish with Status: completed-with-assumptions; the first item of the recap is the confirmation list. Never present an assumed decision as a user decision.

## Finishing

The interview is DONE only when all of these hold:
1. Every Open branch is resolved or explicitly deferred as a flag with an owner.
2. Edge-case probe applied once to the core flow: alternative paths? "Könnte X passieren — was dann?" error cases? permissions/data lifecycle? anything still unquantified ("schnell", "viele")?
3. Closing metaquestion asked: "Was hätte ich fragen sollen, das ich nicht gefragt habe?"
4. Read-back: summarize all decisions in a few lines and get explicit confirmation — corrections go back into the file first.

Then: re-read the capture for contradictions and reconcile, fill the Handoff section, set Status: completed (or completed-with-assumptions), and give a short recap: what's decided, what's flagged, next step → /create-spec.

## Capture file template

Use exactly this structure. Headings stay English (tools depend on them); content in the user's language. Omit sections that would be empty.

```
# {Topic} — sync-us interview
Date: {YYYY-MM-DD} · Status: in-progress | paused | completed | completed-with-assumptions · Goal: {one line} · Ticket: {ID or —}

## Context established (recon, before Q1)
- verified facts with paths/sources — no assumptions

## Summary / key decisions
- [Q1] {decision in one line}   (updated at every checkpoint)

## Open branches
1. ~~{decided branch}~~ → ENTSCHIEDEN (Q2)
2. {open branch} ← → NEXT
3. {open branch} (Folge aus Q2)

## Q&A log

### Q1 — {branch}
- Asked: {question}
- Recommended: {your recommendation}
- Decision: {user's answer, in their words where the wording matters} — or [ASSUMED] {your call + one-line reasoning}
- Folge: {new branches / consequences, if any}
- Flags: {open item → owner}

## Assumed pending confirmation
- [ASSUMED] {decision} (Qn) → confirm with {who}

## Open flags (pending input)
- {item} → {owner who can answer}

## Handoff (input for /create-spec)
- {implementation checklist / key constraints derived from the decisions}
- {rejected alternatives worth recording → become the spec's Out of Scope}
```
