# spec-pipeline

**Claude Code skills that take a feature from vague idea to shippable work.**

```
  💬 sync-us   ──▶   📄 create-spec   ──▶   🔪 slice-it-vertically   ──▶   build it
  interview          write the spec         cut into vertical phases
  docs/sync-us/      docs/specs/            docs/phases/
```

Three skills, one pipeline. You talk, Claude asks the right questions, and every step leaves a plain Markdown file in your repo. You can review, edit and commit those files, or hand them to the next session.

| Skill | What it does | Output |
|---|---|---|
| **`/sync-us`** | Interviews you one question at a time until every design decision is settled. Saves after **every** answer, so you can pause and resume later. | `docs/sync-us/{date}-{topic}.md` |
| **`/create-spec`** | Turns the finished interview (or the current conversation) into a spec. It asks no questions and doesn't make anything up. | `docs/specs/{topic}.md` |
| **`/slice-it-vertically`** | Cuts the spec into **vertical** phases. Each phase works end to end and can be demoed. There are no "backend phase" or "testing phase" slices. | `docs/phases/phase-{N}-{title}.md` |

---

## Table of contents

1. [Installation](#1-installation)
   - [Option A: let Claude install it](#option-a--let-claude-install-it-recommended)
   - [Option B: install it manually](#option-b--install-it-manually)
2. [How it works: a full walkthrough](#2-how-it-works--a-full-walkthrough)
3. [Tips & FAQ](#3-tips--faq)

---

## 1. Installation

Claude Code loads skills from a folder named `skills` inside a `.claude` directory. Each skill is a folder that contains a `SKILL.md`:

```
~/.claude/skills/              ← personal: available in ALL your projects
├── sync-us/
│   └── SKILL.md
├── create-spec/
│   └── SKILL.md
└── slice-it-vertically/
    └── SKILL.md
```

| Scope | Location | Use when |
|---|---|---|
| **Personal** (recommended) | `~/.claude/skills/` (Windows: `%USERPROFILE%\.claude\skills\`) | You want the skills everywhere |
| **Project** | `<your-repo>/.claude/skills/` | You want to commit them so your whole team gets them |

### Option A: let Claude install it (recommended)

Open Claude Code in any directory and paste this:

> Install the Claude Code skills from https://github.com/Dusan2709/spec-pipeline into my personal skills directory (`~/.claude/skills/`). Follow the instructions in the repo's `INSTALL.md`.

That's it. Claude clones the repo into a temp folder and copies the three skill folders to the right place. If you already have a skill with the same name, it asks before overwriting anything. Then it cleans up and confirms the install.

*Want them in a single project instead?* Replace "my personal skills directory" with "this project's `.claude/skills/` directory".

### Option B: install it manually

**macOS / Linux**

```bash
git clone https://github.com/Dusan2709/spec-pipeline.git /tmp/spec-pipeline
mkdir -p ~/.claude/skills
cp -R /tmp/spec-pipeline/skills/* ~/.claude/skills/
rm -rf /tmp/spec-pipeline
```

**Windows (PowerShell)**

```powershell
git clone https://github.com/Dusan2709/spec-pipeline.git $env:TEMP\spec-pipeline
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills" | Out-Null
Copy-Item -Recurse -Force "$env:TEMP\spec-pipeline\skills\*" "$env:USERPROFILE\.claude\skills\"
Remove-Item -Recurse -Force "$env:TEMP\spec-pipeline"
```

**No git?** Click **Code → Download ZIP** on GitHub, unzip it, and copy the three folders from `skills/` into `~/.claude/skills/`.

### Check that it worked

Start a **new** Claude Code session and type `/` to open the command list. `/sync-us`, `/create-spec` and `/slice-it-vertically` should all be listed. You can also run `/skills`.

---

## 2. How it works: a full walkthrough

We'll follow one feature through the whole pipeline: **"Let users share their wishlist via a link."**

The complete files from this example are in [`examples/docs/`](examples/docs/), so you can see exactly what the skills produce.

### Step 1: `/sync-us`: the interview

```
> /sync-us I want users to be able to share their wishlist via a link
```

Claude **looks things up before asking**. It reads your codebase, tickets and docs first, so it never asks something it could find out itself. Then it builds a decision tree and starts the interview:

- **One question per turn**, with clickable options and a marked recommendation.
- **Obvious defaults** are bundled into one question ("I'll assume these, what do you want to change?"), so you don't have to click through ten trivial confirmations.
- **Vague answers are followed up.** If you answer "like usual", Claude asks what "usual" means.
- At the end it asks *"What should I have asked that I didn't?"* and reads all decisions back to you for a final OK.

```
  Q3 — Can the recipient see who already bought an item?
  ─────────────────────────────────────────────────────────
  ❯ 1. Yes, "reserved" without a name (Recommended)
       Prevents duplicate gifts, keeps the surprise.
    2. Yes, with the buyer's name
       More transparent, spoils the surprise for the owner.
    3. No
       Simplest. Risk of duplicate gifts.
```

**The important part: every answer is saved immediately** to a capture file:

```
docs/sync-us/2026-11-16-wishlist-sharing.md
```

#### Pause and resume

You're out of time, or the context is filling up? Just say **"let's pause"**. Claude marks the file as `paused` and sets a `→ NEXT` cursor on the next open question:

```markdown
# Wishlist sharing — sync-us interview
Date: 2026-11-16 · Status: paused · Goal: Owners can share a wishlist via link · Ticket: —

## Open branches
1. ~~Who can open a shared link~~ → DECIDED (Q1)
2. ~~Link lifetime~~ → DECIDED (Q2)
3. Can recipients see what's already bought ← → NEXT
4. Revoking a link
```

Next day, new session, run `/sync-us` again. Claude finds the paused interview, sums up where you left off in 2–3 sentences, and **continues exactly at the cursor**. It never repeats a question you already answered. The same file also survives crashes and context compaction.

> **Delegating:** you can also say *"decide the rest yourself"*. Claude finishes the interview, but marks every decision it made as **`[ASSUMED]`** and lists them for you to confirm. That way you can always tell its guesses from your own decisions.

When the interview is done, the file has `Status: completed` and a **Handoff** section that becomes the input for the spec.
👉 Full example: [`examples/docs/sync-us/2026-11-16-wishlist-sharing.md`](examples/docs/sync-us/2026-11-16-wishlist-sharing.md)

### Step 2: `/create-spec`: write the spec

```
> /create-spec
```

You can run this right after the interview or days later. Claude picks up the newest completed capture on its own. It **doesn't interview you again and doesn't invent anything**:

1. It reads the capture, plus your `docs/guidelines/` and `docs/adr/` if you have them.
2. It shows you a module sketch and a 5-line summary **before** writing.
3. It writes the spec: problem, solution, testable acceptance criteria (`WHEN … THE SYSTEM SHALL …`), decisions **with reasons and rejected alternatives**, code references, out of scope, and open questions.
4. It checks the spec itself: does every decision appear, are the sections consistent, can every acceptance criterion be tested? Anything unresolved is marked `[NEEDS CLARIFICATION]`.

```
docs/specs/wishlist-sharing.md
```

👉 Full example: [`examples/docs/specs/wishlist-sharing.md`](examples/docs/specs/wishlist-sharing.md)

Review the spec before moving on. Errors are cheapest to fix at this stage.

### Step 3: `/slice-it-vertically`: cut it into phases

```
> /slice-it-vertically
```

Claude cuts the spec into **vertical slices**. Each phase goes through all layers (UI → API → DB) and produces something you can **demo**. Phases like "Setup", "Database", "Frontend" or "Testing" are **not allowed**. That work goes into the first slice that needs it.

Every phase must pass three checks:
1. Someone can trigger it and see a result.
2. The result is visibly different from the previous phase.
3. It can be built without any later phase.

Each phase is also labeled **AFK** (an agent can do it alone, because everything can be checked automatically) or **HITL** (needs a human for UX decisions, credentials or approvals), with a one-line reason.

Claude shows you the list first, and writes the files once you confirm:

```
docs/phases/
├── phase-1-share-link-read-only.md   AFK   Owner copies a link, a guest sees the list
├── phase-2-revoke-link.md            AFK   Owner revokes, old link shows "no longer shared"
└── phase-3-reserve-items.md          HITL  Guest marks an item as reserved
```

👉 Full examples: [`examples/docs/phases/`](examples/docs/phases/)

### What your repo looks like at the end

```
your-project/
└── docs/
    ├── sync-us/
    │   └── 2026-11-16-wishlist-sharing.md     ← every decision + why
    ├── specs/
    │   └── wishlist-sharing.md                ← the what & why
    └── phases/
        ├── phase-1-share-link-read-only.md    ← one focused session each
        ├── phase-2-revoke-link.md
        └── phase-3-reserve-items.md
```

Now implement phase by phase, for example with *"Implement docs/phases/phase-1-share-link-read-only.md"* in a fresh session. Each phase file has everything needed: acceptance criteria, verification commands, and what blocks what.

---

## 3. Tips & FAQ

**When should I NOT use this?**
For a small bugfix or a one-day ticket, the interview is overkill. Just ask Claude directly. The pipeline pays off once a feature involves several real design decisions.

**Do I have to run all three?**
No. `/create-spec` also works from a normal design discussion in the chat, and `/slice-it-vertically` works on any spec or plan. Running them in order gives the best results, though.

**What language does it use?**
The skills are written in English, but Claude writes the content in **your** language. The section headings stay English on purpose, so other tools can parse the files.

**The skills mention `plan-ticket`, `spec-to-jira` and `phases-to-multica`. Where are those?**
Those are optional companion skills from my own setup and aren't included here. They're only referenced for routing ("for small tickets use plan-ticket instead"). Everything in this repo works without them.

**Should I commit the `docs/` files?**
Yes. The capture records *why* each decision was made, and that is very useful when someone asks about it six months later.

**How do I update the skills?**
Run the installation again. Option A asks before overwriting, Option B overwrites directly.

---

<sub>Built for [Claude Code](https://claude.com/claude-code). Feedback and PRs welcome.</sub>
