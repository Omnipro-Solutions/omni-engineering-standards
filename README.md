# omni-engineering-standards

A Claude Code plugin that enforces senior-level engineering standards and minimizes common LLM coding mistakes for Omni developer teams.

---

## The Problem

LLMs tend to:
- Jump to implementation without clarifying requirements
- Over-engineer simple solutions
- Modify unrelated code while fixing a bug
- Mark tasks complete without verifying the outcome
- Patch symptoms instead of fixing root causes

This plugin injects behavioral guardrails that push Claude to operate more like a deliberate senior engineer.

---

## Core Principles

| Principle | What it prevents |
|---|---|
| **Plan-First Execution** | Blind implementation, brute-force debugging |
| **Think Before Coding** | Silent assumptions, missed ambiguities |
| **Simplicity First** | Over-abstraction, unnecessary configurability |
| **Surgical Changes** | Scope creep, unrelated refactors |
| **Verification Discipline** | Marking tasks done without proof |
| **Self-Improvement Loop** | Repeating the same mistake class |

---

## Installation

### Option A — Claude Code Plugin (recommended for teams)

```bash
/plugin marketplace add omni-pro/omni-engineering-standards
```

### Option B — Per-project CLAUDE.md

Copy the contents of `CLAUDE.md` into the root `CLAUDE.md` of any project:

```bash
cat CLAUDE.md >> /path/to/your/project/CLAUDE.md
```

### Option C — Global user CLAUDE.md

Add to your global Claude configuration so it applies to every project:

```bash
cat CLAUDE.md >> ~/.claude/CLAUDE.md
```

---

## How It Works

Once active, Claude will:

1. **Enter plan mode** before any non-trivial task and confirm the plan with you before coding.
2. **State assumptions** explicitly and ask when something is unclear.
3. **Scope changes surgically** — only the lines required to complete the task.
4. **Verify outcomes** before marking anything done.
5. **Log lessons** in `tasks/lessons.md` after any correction, so the same mistake doesn't happen twice.

---

## Task File Conventions

| File | Purpose |
|---|---|
| `tasks/todo.md` | Active plan for the current task |
| `tasks/lessons.md` | Accumulated mistake patterns and prevention rules |

---

## Customization

Edit `skills/omni-engineering-standards/SKILL.md` to adapt the guidelines to your team's workflow. Common adjustments:

- Swap `tasks/todo.md` for your issue tracker
- Add team-specific coding standards (linting rules, PR conventions)
- Tune the "non-trivial threshold" for plan mode

---

## License

MIT © Julián Valdés — [julian.valdes@omni.pro](mailto:julian.valdes@omni.pro)
