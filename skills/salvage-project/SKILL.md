# Salvage Project

You are the salvage-project skill. Pick your mode by checking for `salvage/manifest.yaml`
in the current working directory:

- **Absent** → Salvage mode (Act 1): recover requirements and emit the bundle.
- **Present** → Accept mode (Act 3): set up the fresh project from the bundle.

---

## Salvage mode

Recover the real requirements from a project that went down a bad path and emit
a portable spec to rebuild simpler.

Run four stages in order. Ask one question at a time. Write nothing until Stage 4.

### Stage 1 — Intake

Ask these four questions, one at a time, waiting for the answer before continuing:

1. What should the app do? (one sentence)
2. Which features caused the most trouble?
3. Where did the previous agent resist your changes?
4. What is your maintenance ceiling — which languages, tools, and build complexity
   can you actually own?

### Stage 2 — Code archaeology

Read the existing code. Separate:
- **Keep** — genuine behaviors the app must have (what it does, not how)
- **Drop** — incidental tech decisions (the framework, the architecture pattern,
  the toolchain)

Produce a feature inventory. List complexity smells. Present both to the developer
and confirm before continuing.

### Stage 3 — Architecture interview

Use the `simplicity-guide` skill to choose each stack layer one at a time:

- data store
- frontend
- realtime
- auth
- jobs
- deploy

For each: identify the lowest rung that meets a stated requirement. Climb only when
a written requirement forces it. Do not exceed the maintenance ceiling.

### Stage 4 — Emit the bundle

Create a `salvage/` directory in the project root with these six files:

**`salvage/manifest.yaml`**
```yaml
bundle_version: "1"
app: "<one-line app name>"
stack: "<chosen stack summary>"
```

**`salvage/requirements.md`**
The feature inventory from Stage 2. Behavioral requirements only — no tech
decisions. Each item in the form: "The app [does X] so that [user/outcome]."

**`salvage/tech-stack.md`**
Each stack choice with its simplicity-guide rung and rationale. Format:
```
## <layer>
- Rung: <rung name>
- Rationale: "<filled rationale template>"
- Rejected: <what was considered and discarded, and why>
```

**`salvage/design-notes.md`**
How the pieces fit together. Deliberately brief — orientation only, not a spec.
Three to five paragraphs maximum.

**`salvage/anti-patterns.md`**
The failed decisions from the previous attempt, each with: what was done, why it
failed, and "DO NOT reintroduce." Format:
```
## <pattern name>
**What happened:** ...
**Why it failed:** ...
**DO NOT reintroduce** because ...
```

**`salvage/CLAUDE.md`**
Fill in the template from `templates/CLAUDE.md` with:
- `{{stack}}` → the chosen stack summary
- `{{requirements}}` → one-line requirements summary
- `{{skill_level}}` → maintenance ceiling description from Stage 1

After writing all six files, confirm the bundle is complete and explain Act 2:
create a clean repo and drop the `salvage/` directory into it.

---

## Accept mode

The bundle exists. Set up the fresh project.

1. **Greet** — read `salvage/manifest.yaml`; greet with
   `"<app> on <stack> — ready to set up."` Ask for confirmation before touching
   anything.

2. **Safety checks**
   - Fresh-target check: warn if the repo contains files beyond `.git`, `salvage/`,
     and `README.md`.
   - Per-file no-clobber: for each of the five files to be written, warn if it
     already exists at the destination.

3. **Detect superpowers** — check whether the `superpowers` plugin is available.

4. **Copy canonical docs** to the repo root:
   - `salvage/requirements.md` → `requirements.md`
   - `salvage/tech-stack.md` → `tech-stack.md`
   - `salvage/design-notes.md` → `design-notes.md`
   - `salvage/anti-patterns.md` → `anti-patterns.md`
   - `salvage/CLAUDE.md` → `CLAUDE.md`

   Leave `salvage/manifest.yaml` in `salvage/`.

5. **Hand off**
   - If superpowers is available: hand the bundle to its `writing-plans` →
     `executing-plans` workflow.
   - If not: build feature-by-feature from `requirements.md`, simplest-first,
     confirming each feature works before starting the next.
