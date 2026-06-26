# salvage

**Start over, simpler — without losing what you learned.**

A Claude Code plugin for the moment a project has gone down a bad path: the
wrong tech stack, over-engineered for what it actually needs, or built on
requirements that turned out to be incomplete. Instead of fighting the existing
code, `salvage` recovers the *real* requirements — now that you know more — and
writes a portable spec to rebuild on the **simplest stack you can actually
maintain**. A fresh agent then implements that spec in a clean project.

It is the inverse of the `handoff` plugin. handoff says *"continue this work."*
salvage says *"this approach failed — keep the intent, drop the decisions."*

salvage produces **documents only**. It does not scaffold or generate code in
the broken project. The recovered spec is the product; building from it happens
later, in a fresh repo.

---

## The three-act lifecycle

salvage runs across two repositories, with you moving a bundle between them.

1. **Act 1 — in the broken project (`/salvage`).** A four-stage interview:
   - **Intake** — what the app is supposed to do, what fought you, and your
     *maintenance ceiling* (the tools you can actually own — a hard cap on the
     stack).
   - **Code archaeology** — read the existing code to separate genuine behavior
     to *keep* from incidental tech decisions to *drop*.
   - **Architecture interview** — choose the stack one fork at a time, starting
     at the simplest rung and climbing only when a stated requirement forces it.
   - **Emit the bundle** — write a self-contained `salvage/` bundle to the
     project root.

   Stages 1–3 only read and ask. Nothing is written until the final stage, and
   nothing is written into a new project yet.

2. **Act 2 — the move.** You create a clean, empty git repo and drop the
   `salvage/` bundle into it.

3. **Act 3 — in the new project (accept).** The `salvage-project` skill
   auto-detects the bundle on session start, confirms before touching anything,
   writes the canonical project docs to the repo root, and transitions straight
   into the build.

---

## Install

salvage is distributed as a Claude Code plugin. Installation is two commands,
run from inside Claude Code.

**1. Add this repo as a marketplace:**

```
/plugin marketplace add maludb/salvage-project
```

**2. Install the plugin:**

```
/plugin install salvage@salvage
```

### Entry points

- **`/salvage`** — the guided command. Run it inside the broken project to start
  Act 1.
- **The `salvage-project` skill** — natural-language triggerable. Saying things
  like *"salvage this project"*, *"start over simpler"*, or *"rebuild this"*
  invokes the same flow.

### Local development

```bash
git clone https://github.com/maludb/salvage-project.git
claude --plugin-dir ./salvage-project
```

---

## What's in the bundle

Act 1 writes a `salvage/` directory containing six files:

| File | What it holds |
| --- | --- |
| `manifest.yaml` | Bundle metadata + the detection file Act 3 looks for |
| `requirements.md` | The recovered "keep" inventory — behavioral requirements only |
| `tech-stack.md` | The chosen stack with rationale for each decision |
| `design-notes.md` | How the pieces fit together — orientation, not a spec |
| `anti-patterns.md` | The failed decisions from last time, marked "do NOT reintroduce" |
| `CLAUDE.md` | Curated build principles with the project's stack and skill level filled in |

---

## How it's built

```
salvage/
├── .claude-plugin/plugin.json
├── commands/
│   └── salvage.md               # /salvage — runs Act 1
├── skills/
│   ├── salvage-project/         # salvage mode (Act 1) + accept mode (Act 3)
│   │   └── SKILL.md
│   └── simplicity-guide/        # reference consulted during architecture interview
│       ├── SKILL.md
│       └── references/          # data-store, frontend, realtime, auth, jobs, deploy
├── templates/
│   └── CLAUDE.md                # curated principles + project-specific slots
└── README.md
```

---

## License

[MIT](LICENSE) © Edward Honour

Source: https://github.com/maludb/salvage-project
