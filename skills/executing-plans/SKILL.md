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

**PASS:** UEP verification succeeds → commit, move to next task

**ASK:** UEP verification fails → stop and request help using required template

**No other outcomes are valid:**
- ❌ No "blocked" — either fix it or ASK
- ❌ No "skipped" — every task must be verified
- ❌ No "partial success" — it works or it doesn't
- ❌ No "tests failed but continuing" — stop and ASK
- ❌ No workarounds — verify via the designated UEP, not shortcuts

## Verification File Management

**At the completion of each phase**, create or append to the verification file.

**File location:** `docs/plans/YYYY-MM-DD-<feature-name>-test-verification.md`
(Same date prefix as the implementation plan)

**If file doesn't exist (first phase)**, create it with header:

```markdown
# [Feature Name] Test Verification

> UEP Type: [From plan header]
> Verification Method: [From plan header]

---
```

**At each phase completion**, append:

```markdown
## Phase N: [Phase Name]
**Verification performed:** [Today's date]
**What was tested:**
- [Specific actions taken to verify via UEP]
- [Commands run, pages visited, dashboards checked]
- [Evidence observed]
**Result:** PASS
```

**The "What was tested" section must be specific enough that someone could reproduce the verification.**

**Do not:**
- Write vague summaries ("tested the feature")
- Skip the verification file when a phase completes
- Overwrite previous phases (append only)

## UEP-Appropriate Verification

**All task verification must use the project's designated User Entrypoint.**

**Web Application (Playwright):**
- ✅ Playwright navigation and assertions
- ✅ Playwright screenshots for evidence
- ❌ curl or direct API calls as final verification

**CLI Tool:**
- ✅ Command execution with output capture
- ✅ Exit code verification
- ❌ Unit tests alone without running actual commands

**API Service:**
- ✅ curl/httpie requests with response capture
- ✅ Test client assertions
- ❌ "I checked the code" without actual requests

**Cloud Dashboard:**
- ✅ Console/dashboard state verification
- ✅ API queries to check resource state (e.g., `terraform show`, `aws cli`)
- ❌ "I applied it" without verifying result

**Library/SDK:**
- ✅ Test suite execution with output
- ✅ Example consumer script execution
- ❌ "Tests pass" without showing output

**The test must match how users actually interact.** Verify via the UEP, not via shortcuts.

## Required ASK Template

**When UEP verification fails, use this exact format:**

```markdown
## Task Failed: [Task Name]

**What I was trying to verify:**
[The user-facing outcome, e.g., "User can see empty recipe list"]

**UEP Type:** [Web Application / CLI Tool / API Service / Cloud Dashboard / Library/SDK]

**What happened:**
[Specific failure, e.g., "Command returned exit code 1 instead of 0"]

**Evidence:**
- Verification output: [paste actual output]
- Screenshot/state: [if applicable]
- Error messages: [if any]

**What I tried:**
1. [First attempt and result]
2. [Second attempt and result]

**What I need from you:**
[Specific ask, e.g., "Is the database running? I see a connection refused error."]
```

**You must provide:**
- Specific failure description (not "it didn't work")
- Evidence from UEP verification (output, screenshots, state)
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
- **UEP verification is mandatory** — verify via the designated User Entrypoint
- **Binary outcomes only** — PASS or ASK, nothing else
- **Use the ASK template** — specific, structured, with evidence
- **Update verification file at phase completion** — append semantic summary
- Reference skills when plan says to
- Between batches: just report and wait
- **Never skip, block, or work around failed verification**
- Never start implementation on main/master branch without explicit user consent

## Integration

**Required workflow skills:**
- **superpowers:using-git-worktrees** - REQUIRED: Set up isolated workspace before starting
- **superpowers:writing-plans** - Creates the plan this skill executes
- **superpowers:finishing-a-development-branch** - Complete development after all tasks
