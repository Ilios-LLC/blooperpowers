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

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>-implementation.md`

**Verification tracking:** Execution skills (executing-plans, subagent-driven-development) will create and maintain `docs/plans/YYYY-MM-DD-<feature-name>-test-verification.md` at phase completion. This skill does not create that file.

**Critical:** The last planned task in each phase MUST be complete testing of user-facing outcomes using the designated UEP Verification Method.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**
- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## User-Testable Phase Structure

**Every phase must leave the project in a user-testable state via the designated UEP.**

**The constraint:** No "infrastructure now, user-facing later" phases. Each phase must include work that makes it verifiable through the User Entrypoint.

**Web Application example:**
```
Phase 1: User can see an empty recipe list
  (schema + API + basic list UI)
Phase 2: User can add a recipe via form
  (create endpoint + form UI)
```

**CLI Tool example:**
```
Phase 1: User can run `mytool --help` and see available commands
  (argument parser + help text)
Phase 2: User can run `mytool init` and see project scaffold created
  (init command + file generation)
```

**Cloud Dashboard example:**
```
Phase 1: User can see new resource in Terraform Cloud plan output
  (terraform config + plan verification)
Phase 2: User can see resource deployed after apply
  (apply + dashboard verification)
```

**Infrastructure bundling rule:** Infrastructure work cannot be its own phase. Bundle it with the first feature that makes it user-visible via the UEP.

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use blooperpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

**User Entrypoint:** [From design doc - e.g., "Web Application", "CLI Tool", "Cloud Dashboard"]

**Verification Method:** [From design doc - e.g., "Playwright browser testing", "Command execution + output checking", "Terraform Cloud workspace verification"]

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

**Step 5: UEP verification (REQUIRED)**

Verify user-facing outcome via the project's User Entrypoint:

*For Web Application:*
- Navigate to: [URL]
- Action: [What to do]
- Expected: [What user should see]

*For CLI Tool:*
- Run: `[command with args]`
- Expected output: [What user should see]

*For API Service:*
- Request: `curl -X [METHOD] [URL] -d '[data]'`
- Expected response: [Status code and body]

*For Cloud Dashboard:*
- Check: [Dashboard/console location]
- Expected state: [What should be visible]

*For Library/SDK:*
- Run: `[test command or example script]`
- Expected: [Output or behavior]

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
