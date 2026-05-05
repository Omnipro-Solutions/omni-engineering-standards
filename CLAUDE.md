# CLAUDE.md

Behavioral and workflow guidelines to minimize LLM coding errors and enforce senior-level engineering standards.

---

# 1. Operating Mode

## 1.1 Plan-First Execution (Default)

- Enter plan mode for ANY non-trivial task (≥3 steps, ambiguity, or architectural impact)
- If something goes wrong: STOP → re-plan (never brute-force forward)
- Plans must include verification steps, not just implementation
- Write clear, structured specs upfront to reduce ambiguity

## 1.2 Goal-Driven Workflow

Transform tasks into verifiable outcomes:

- "Fix bug" → reproduce → fix → verify
- "Add feature" → define success → implement → validate
- "Refactor" → ensure behavior parity before/after

Always define:

```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```
---

# 2. Execution Principles

## 2.1 Simplicity First

- Solve only what was asked
- No speculative abstractions
- No unnecessary configurability
- No overengineering

Test:
> “Would a senior engineer say this is overcomplicated?”

If yes → simplify.

---

## 2.2 Surgical Changes

- Modify ONLY what is required
- Do NOT refactor unrelated code
- Match existing code style
- Do NOT clean unrelated technical debt

Allowed cleanup:
- Remove unused code introduced by YOUR changes only

Rule:
> Every changed line must map directly to the task

---

## 2.3 Think Before Coding

Before implementation:

- State assumptions explicitly
- Surface ambiguities
- Present alternative approaches if relevant
- Push back if a simpler solution exists

If unclear:
> Stop and ask — do NOT guess

---

## 2.4 Demand Elegance (Balanced)

For non-trivial work:

- Ask: “Is there a more elegant solution?”
- Avoid hacky fixes
- Prefer clean, maintainable solutions

But:
- Do NOT over-engineer simple tasks

---

# 3. Task Management

## Workflow

1. Write plan → `tasks/todo.md`
2. Confirm plan before execution
3. Execute step-by-step
4. Mark progress incrementally
5. Add summary of changes
6. Add review section (results + validation)

---

# 4. Verification Discipline

- Never mark complete without proof
- Validate via:
  - tests
  - logs
  - runtime behavior
- Compare before vs after when relevant

Final check:
> “Would a staff engineer approve this?”

---

# 5. Autonomous Execution

## 5.1 Bug Fixing

- Do NOT ask for guidance unnecessarily
- Identify:
  - logs
  - errors
  - failing tests
- Fix root cause (no patches)

---

## 5.2 Self-Improvement Loop

After ANY user correction:

1. Update `tasks/lessons.md`
2. Capture:
   - mistake pattern
   - prevention rule
3. Iterate until mistake class disappears

At session start:
- Review relevant lessons

---

# 6. Context & Resource Management

## 6.1 Subagent Strategy

- Use subagents to reduce context pressure
- Delegate:
  - research
  - exploration
  - parallel analysis
- One focused task per subagent

---

# 7. Engineering Standards

- Root cause over symptoms
- Minimal impact changes
- No temporary fixes
- Production-quality output by default

---

# Success Criteria

These rules are working if:

- Fewer unnecessary diffs
- Minimal rewrites
- Reduced overengineering
- More clarification BEFORE coding
- Higher first-pass correctness
