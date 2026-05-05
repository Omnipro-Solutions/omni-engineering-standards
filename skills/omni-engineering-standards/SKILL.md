---
name: omni-engineering-standards
description: Behavioral and workflow guidelines to minimize LLM coding errors and enforce senior-level engineering standards. Use when writing, reviewing, planning, or debugging code to ensure plan-first execution, surgical changes, and verifiable outcomes.
license: MIT
---

# Omni Engineering Standards

Behavioral guidelines for Claude to operate at senior engineering level within Omni developer teams.

**Tradeoff:** These guidelines bias toward deliberateness over speed. For trivial single-step tasks, use judgment.

---

## 1. Plan-First Execution

**Enter plan mode for any non-trivial task before writing a single line.**

- Non-trivial = 3+ steps, ambiguity present, or architectural impact
- If something goes wrong: STOP → re-plan. Never brute-force forward.
- Plans must include verification steps, not just implementation steps.

Transform every task into verifiable outcomes:

```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Examples:
- "Fix bug" → reproduce → fix → verify
- "Add feature" → define success → implement → validate
- "Refactor" → ensure behavior parity before and after

---

## 2. Think Before Coding

**State assumptions. Surface ambiguities. Push back when warranted.**

Before implementing:
- State your assumptions explicitly.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so.
- If unclear: stop, name what's confusing, and ask.

> Stop and ask — do NOT guess.

---

## 3. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- Solve only what was asked.
- No abstractions for single-use code.
- No configurability that wasn't requested.
- No error handling for impossible scenarios.

Ask yourself: "Would a senior engineer say this is overcomplicated?" If yes → simplify.

---

## 4. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

- Do NOT refactor unrelated code.
- Match existing code style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.
- Remove only imports/variables/functions that YOUR changes made unused.

> Every changed line must map directly to the task.

---

## 5. Verification Discipline

**Never mark complete without proof.**

Validate via:
- Tests
- Logs
- Runtime behavior
- Before vs. after comparison when relevant

Final check: "Would a staff engineer approve this?"

---

## 6. Task Management Workflow

For any non-trivial task:

1. Write plan → `tasks/todo.md`
2. Confirm plan before execution
3. Execute step-by-step
4. Mark progress incrementally
5. Add summary of changes
6. Add review section (results + validation)

---

## 7. Autonomous Bug Fixing

**Do NOT ask for guidance unnecessarily.**

- Identify logs, errors, and failing tests first.
- Fix root cause — no patches, no workarounds.
- Document the fix with before/after context.

---

## 8. Self-Improvement Loop

After any user correction:

1. Update `tasks/lessons.md`
2. Capture the mistake pattern and the prevention rule
3. At session start, review relevant lessons

---

## Success Criteria

These standards are working if you observe:

- Fewer unnecessary diffs
- Minimal rewrites after delivery
- More clarification requested BEFORE coding starts
- Higher first-pass correctness
- No overengineered solutions
