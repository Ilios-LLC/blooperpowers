---
name: executing-plans
description: Use when you have a written implementation plan to execute in a separate session with review checkpoints
---

# Executing Plans

## Overview

Load plan, review critically, execute tasks in batches, report for review between batches.

**Core principle:** Batch execution with checkpoints for architect review.

**Announce at start:** "I'm using the executing-plans skill to implement this plan."

## The Process

### Step 1: Load and Review Plan
1. Read plan file
2. Review critically - identify any questions or concerns about the plan
3. If concerns: Raise them with your human partner before starting
4. If no concerns: Create TodoWrite and proceed

### Step 2: Execute Batch
**Default: First 3 tasks**

For each task:
1. Mark as in_progress
2. Follow each step exactly (plan has bite-sized steps)
3. Run verifications as specified
4. Mark as completed

### Step 3: Report
When batch complete:
- Show what was implemented
- Show verification output
- Say: "Ready for feedback."

### Step 4: Continue
Based on feedback:
- Apply changes if needed
- Execute next batch
- Repeat until complete

### Step 5: Complete Development

After all tasks complete and verified:
- Announce: "I'm using the finishing-a-development-branch skill to complete this work."
- **REQUIRED SUB-SKILL:** Use superpowers:finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## Binary Outcomes (REQUIRED)

**Every task ends in one of two states:**

**PASS:** Playwright verification succeeds → commit, move to next task

**ASK:** Playwright verification fails → stop and request help using required template

**No other outcomes are valid:**
- ❌ No "blocked" — either fix it or ASK
- ❌ No "skipped" — every task must be verified
- ❌ No "partial success" — it works or it doesn't
- ❌ No "tests failed but continuing" — stop and ASK
- ❌ No workarounds — no curl, no API tests, no unit-test-only verification

## Playwright-Only Verification (Web Apps)

**For web applications, all task verification must use Playwright browser testing.**

**Allowed:**
- ✅ Playwright navigation and assertions
- ✅ Playwright screenshots for evidence
- ✅ Playwright console log capture

**Not allowed as final verification:**
- ❌ curl or wget
- ❌ Direct API calls
- ❌ Unit tests alone (unit tests are in addition to, not instead of)
- ❌ "I checked the code and it looks correct"
- ❌ Postman or similar tools

**The test must match how users actually interact.** If users use a browser, verify with a browser.

## Required ASK Template

**When Playwright verification fails, use this exact format:**

```markdown
## Task Failed: [Task Name]

**What I was trying to verify:**
[The user-facing outcome, e.g., "User can see empty recipe list"]

**What happened:**
[Specific failure, e.g., "Page shows 500 error instead of recipe list"]

**Evidence:**
- Playwright output: [paste]
- Screenshot: [if available]
- Console errors: [if any]

**What I tried:**
1. [First attempt and result]
2. [Second attempt and result]

**What I need from you:**
[Specific ask, e.g., "Is the database running? I see a connection refused error."]
```

**You must provide:**
- Specific failure description (not "it didn't work")
- Evidence from Playwright (output, screenshots, console)
- What you already tried
- A specific, actionable ask

## When to Revisit Earlier Steps

**Return to Review (Step 1) when:**
- Partner updates the plan based on your feedback
- Fundamental approach needs rethinking

**Don't force through blockers** - stop and ask.

## Remember
- Review plan critically first
- Follow plan steps exactly
- **Playwright verification is mandatory for web apps** — no exceptions
- **Binary outcomes only** — PASS or ASK, nothing else
- **Use the ASK template** — specific, structured, with evidence
- Reference skills when plan says to
- Between batches: just report and wait
- **Never skip, block, or work around failed verification**
- Never start implementation on main/master branch without explicit user consent

## Integration

**Required workflow skills:**
- **superpowers:using-git-worktrees** - REQUIRED: Set up isolated workspace before starting
- **superpowers:writing-plans** - Creates the plan this skill executes
- **superpowers:finishing-a-development-branch** - Complete development after all tasks
