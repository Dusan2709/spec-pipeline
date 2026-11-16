# Installation instructions for AI agents

> **Human?** You probably want the [README](README.md). This file is for Claude Code (or another agent) when a user asks it to install these skills.

## Goal

Copy the three skill folders from this repo's `skills/` directory into the user's Claude Code skills directory, without silently overwriting anything.

Skills to install:
- `skills/sync-us/`
- `skills/create-spec/`
- `skills/slice-it-vertically/`

## Steps

1. **Pick the target directory.**
   - Default (user said "personal", "global", or didn't say): `~/.claude/skills/`. On Windows: `%USERPROFILE%\.claude\skills\`.
   - User said "this project" / "project-level": `<current project root>/.claude/skills/`.
   - Create the directory if it doesn't exist.

2. **Get the files.** Clone into a temporary directory, not into the user's working directory:
   ```bash
   git clone --depth 1 https://github.com/Dusan2709/spec-pipeline.git "$TMPDIR/spec-pipeline"
   ```
   If `git` is unavailable, download `https://github.com/Dusan2709/spec-pipeline/archive/refs/heads/main.zip` and unzip it into a temp directory.

3. **Check for conflicts.** For each of the three skill names, check whether `<target>/<skill-name>/` already exists.
   - If it exists, show the user which ones would be replaced and **ask before overwriting**. Offer to back up the existing folder to `<skill-name>.bak` first.
   - If it doesn't exist, proceed.

4. **Copy.** Copy each skill folder as a whole (it must stay `<target>/<skill-name>/SKILL.md`, not a flat `SKILL.md` in `<target>`):
   ```bash
   cp -R "$TMPDIR/spec-pipeline/skills/sync-us" \
         "$TMPDIR/spec-pipeline/skills/create-spec" \
         "$TMPDIR/spec-pipeline/skills/slice-it-vertically" \
         <target>/
   ```

5. **Verify.** Confirm that these three files exist and start with a `---` frontmatter block containing `name:`:
   - `<target>/sync-us/SKILL.md`
   - `<target>/create-spec/SKILL.md`
   - `<target>/slice-it-vertically/SKILL.md`

6. **Clean up.** Delete the temporary clone.

7. **Report to the user** in their language:
   - where the skills were installed
   - that they may need to start a new Claude Code session for the skills to appear
   - how to start: `/sync-us <topic>` → `/create-spec` → `/slice-it-vertically`
   - a link to the README walkthrough: https://github.com/Dusan2709/spec-pipeline#2-how-it-works--a-full-walkthrough

## Do not

- Do not copy `README.md`, `INSTALL.md` or `examples/` into the skills directory. Only the three skill folders.
- Do not modify the skill files during installation.
- Do not overwrite existing skills without explicit confirmation.
