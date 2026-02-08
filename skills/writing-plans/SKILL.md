---
name: writing-plans
description: Use when you have a spec or requirements for a multi-step task, before touching code
---

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase and questionable taste. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

Assume they are a skilled developer, but know almost nothing about our toolset or problem domain. Assume they don't know good test design very well.

**Announce at start:** "I'm using the writing-plans skill to create the implementation plan."

**Context:** This should be run in a dedicated worktree (created by brainstorming skill).

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`
**After plans are saved** Save a test verification file at `docs/plans/YYYY-MM-DD-<feature-name>-test-verification.md`. It should be a checklist file, with a simple empty checkbox for each planned phase.
**Critical** The last planned task in each phase MUST be the complete testing of user-facing outcomes using the determined Verification Method, and the update of the phase in the docs/plans/YYYY-MM-DD-<feature-name>-test-verification.md to complete.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## User-Testable Phase Structure (Web Apps)

**Every phase must leave the app in a user-testable state.**

**The constraint:** No "backend now, frontend later" phases. Each phase must include UI work that makes it verifiable through the browser.

**Instead of:**
```
Phase 1: Set up database schema for recipes
Phase 2: Add API endpoints for recipes
Phase 3: Build recipe list UI
```

**Structure as:**
```
Phase 1: User can see an empty recipe list
  (schema + API + basic list UI)
Phase 2: User can add a recipe via form
  (create endpoint + form UI)
Phase 3: User can delete a recipe
  (delete endpoint + delete button + confirmation)
```

**Infrastructure bundling rule:** Infrastructure work cannot be its own phase. Bundle it with the first feature that makes it user-visible.

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use blooperpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

**User Entrypoint:** [From design doc - e.g., "Web application"]

**Verification Method:** [e.g., "Playwright browser testing - each phase verified through UI"]

**User-Facing Capabilities:** [List from design doc]

---
```

## Task Structure

```markdown
### Task N: [User-Facing Outcome]

**What the user can do after this task:** [e.g., "User can see an empty recipe list"]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

```python
def test_specific_behavior():
    result = function(input)
    assert result == expected
```

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

```python
def function(input):
    return expected
```

**Step 4: Run test to verify it passes**

Run: `pytest tests/path/test.py::test_name -v`
Expected: PASS

**Step 5: Playwright verification (REQUIRED for web apps)**

Run Playwright to verify user-facing outcome:
- Navigate to: [URL]
- Action: [What to do]
- Expected: [What user should see]
- Pass criteria: [Specific visible elements, no console errors]

**Step 6: Commit**

```bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
```
```

## Verification Red Flags

When executing verification tasks, watch for these shortcuts that lead to incomplete testing:

| Thought Pattern | Reality |
|-----------------|---------|
| "The happy path works, that's enough" | If you're only verifying things work, not that they correctly reject invalid input, you're skipping half the tests. |
| "The result was unclear but let's move on" | An unclear or ambiguous result is not a passing result. Investigate or flag it. |
| "I wrote the CSS, it probably works" | If it can't be seen, it wasn't tested. Visual/CSS changes need visual verification. |
| "I tested pan, zoom probably works too" | Testing one variant doesn't verify others. Each behavior needs its own verification. |
| "I remember the checklist items" | Copy the verification checklist into your response and physically check off each item as you test it. Working from memory leads to skipped items. |

## Remember
- Exact file paths always
- Complete code in plan (not "add validation")
- Exact commands with expected output
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD, frequent commits

## Execution Handoff

After saving the plan, offer execution choice:

**"Plan complete and saved to `docs/plans/<filename>.md`. Two execution options:**

**1. Subagent-Driven (this session)** - I dispatch fresh subagent per task, review between tasks, fast iteration

**2. Parallel Session (separate)** - Open new session with executing-plans, batch execution with checkpoints

**Which approach?"**

**If Subagent-Driven chosen:**
- **REQUIRED SUB-SKILL:** Use blooperpowers:subagent-driven-development
- Stay in this session
- Fresh subagent per task + code review

**If Parallel Session chosen:**
- Guide them to open new session in worktree
- **REQUIRED SUB-SKILL:** New session uses blooperpowers:executing-plans
