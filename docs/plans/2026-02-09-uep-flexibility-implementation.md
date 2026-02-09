# UEP Flexibility Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use blooperpowers:executing-plans to implement this plan task-by-task.

**Goal:** Make skills UEP-agnostic by checking CLAUDE.md first, inferring from codebase if missing, and tracking verification per-phase in execution skills.

**Architecture:** Update 4 skill files to support flexible UEP discovery and verification tracking. brainstorming handles discovery, writing-plans references it, executing-plans and subagent-driven-development handle verification file creation/append.

**Tech Stack:** Markdown skill files

**User Entrypoint:** Library/SDK (this is a skills plugin repository)

**Verification Method:** Manual skill invocation testing + unit tests if applicable

**User-Facing Capabilities:**
- Skills detect UEP from CLAUDE.md or infer from codebase
- Skills support 6 UEP types (Web, CLI, API, Cloud Dashboard, Library/SDK, Other)
- Execution skills create/append verification file at phase completion
- Verification summaries are semantic with evidence

---

## Phase 1: Update brainstorming skill with flexible UEP discovery

**User-facing outcome:** brainstorming skill checks CLAUDE.md first, infers UEP if missing, presents 6 options, offers to persist.

---

### Task 1.1: Replace web-app-centric UEP section in brainstorming

**What changes after this task:** brainstorming skill has new UEP discovery section that checks CLAUDE.md first

**Files:**
- Modify: `skills/brainstorming/SKILL.md:23-41`

**Step 1: Read current file to confirm line numbers**

Verify lines 23-41 contain the current "Identifying user entrypoint" section.

**Step 2: Replace the UEP section**

Replace lines 23-41 with:

```markdown
**Identifying user entrypoint (REQUIRED for all projects):**

1. **Check CLAUDE.md first** - Look for existing `## User Entrypoint` block:
   ```markdown
   ## User Entrypoint
   Type: Web Application
   Verification: Playwright browser testing at http://localhost:3000
   ```

2. **If found** - Confirm with user:
   > "I see your UEP is **[Type]** with verification via [Verification]. Is that correct?"

3. **If not found** - Explore codebase to infer type:
   - Has `package.json` with React/Vue/Svelte/Next → Web Application
   - Has `main.go` or `cmd/` directory → CLI Tool
   - Has `terraform/` or `.tf` files → Cloud Dashboard (Terraform)
   - Has `openapi.yaml` or API routes only → API Service
   - Has `setup.py`/`pyproject.toml` with library structure → Library/SDK
   - None of the above → Ask user

4. **Present inference**:
   > "This looks like a **[Type]** based on [evidence]. Is that correct?"

5. **If user says no**, offer options:
   - Web Application (Playwright browser testing)
   - CLI Tool (command execution + output verification)
   - API Service (endpoint testing via curl/test client)
   - Cloud Dashboard (Terraform Cloud, Vercel, AWS, etc.)
   - Library/SDK (unit tests + example consumer code)
   - Other (describe your verification approach)

6. **Offer to persist**:
   > "Would you like me to add this to your CLAUDE.md so future sessions remember it?"

- Store UEP type and verification method in design document for use by writing-plans
```

**Step 3: Verify the edit**

Read the file to confirm the new section is in place and well-formed.

**Step 4: Commit**

```bash
git add skills/brainstorming/SKILL.md
git commit -m "feat(brainstorming): add flexible UEP discovery with CLAUDE.md lookup"
```

---

### Task 1.2: Update user interaction discovery section

**What changes after this task:** The "Discovering user interactions" section is UEP-agnostic

**Files:**
- Modify: `skills/brainstorming/SKILL.md:32-41` (line numbers shifted after Task 1.1)

**Step 1: Read file to find "Discovering user interactions" section**

Locate the section that starts with `**Discovering user interactions (REQUIRED):**`

**Step 2: Update the section to be UEP-agnostic**

Replace the current section with:

```markdown
**Discovering user interactions (REQUIRED):**
- For every feature request, ask: "How will users interact with [feature]?"
  - Options vary by UEP type (UI forms/buttons, CLI commands, API endpoints, dashboard checks, etc.)
- If user mentions functionality without specifying interaction, pitch missing components:
  > "For users to actually use [feature], you'd likely need:
  > - [List specific interaction points based on UEP type]
  >
  > Should I include these in the design?"
- Before proceeding to design, confirm complete user-facing scope:
  > "So when this is done, a user will be able to: [list capabilities]. All phases will be verified using [Verification Method]. Correct?"
```

**Step 3: Verify the edit**

Read the file to confirm changes are correct.

**Step 4: Commit**

```bash
git add skills/brainstorming/SKILL.md
git commit -m "feat(brainstorming): make user interaction discovery UEP-agnostic"
```

---

### Task 1.3: Update design document requirements

**What changes after this task:** Design doc requirements reflect flexible UEP

**Files:**
- Modify: `skills/brainstorming/SKILL.md` - "After the Design" section

**Step 1: Locate "After the Design" section**

Find the section with design document requirements.

**Step 2: Update the design document MUST include list**

Replace the current list with:

```markdown
- Design document MUST include:
  - **User Entrypoint type:** (e.g., "CLI Tool", "Web Application", "Cloud Dashboard")
  - **Verification method:** How phases will be verified (e.g., "Command execution + output checking")
  - **User-facing capabilities:** List of what users can do when complete
  - **Phase verification approach:** How each phase will be tested from user perspective
```

**Step 3: Verify the edit**

Read the file to confirm changes.

**Step 4: Commit**

```bash
git add skills/brainstorming/SKILL.md
git commit -m "feat(brainstorming): update design doc requirements for flexible UEP"
```

---

### Task 1.4: Phase 1 verification and test-verification.md update

**What the user can do after this task:** Phase 1 is verified and documented

**Files:**
- Create/Append: `docs/plans/2026-02-09-uep-flexibility-test-verification.md`

**Step 1: Verify brainstorming skill changes**

Read `skills/brainstorming/SKILL.md` and verify:
- [ ] UEP discovery section checks CLAUDE.md first
- [ ] 6 UEP types are listed with verification methods
- [ ] Offers to persist UEP to CLAUDE.md
- [ ] User interaction discovery is UEP-agnostic
- [ ] Design doc requirements include UEP type and verification method

**Step 2: Create/append verification file**

If `docs/plans/2026-02-09-uep-flexibility-test-verification.md` doesn't exist, create it with header. Then append Phase 1 verification:

```markdown
# UEP Flexibility Test Verification

> UEP Type: Library/SDK
> Verification Method: Manual skill file inspection + structure verification

---

## Phase 1: Update brainstorming skill with flexible UEP discovery
**Verification performed:** [DATE]
**What was tested:**
- Read skills/brainstorming/SKILL.md
- Confirmed UEP discovery section checks CLAUDE.md first
- Confirmed 6 UEP types listed with verification approaches
- Confirmed offer to persist UEP to CLAUDE.md
- Confirmed user interaction discovery is UEP-agnostic
- Confirmed design doc requirements updated
**Result:** PASS/FAIL
```

**Step 3: Commit**

```bash
git add docs/plans/2026-02-09-uep-flexibility-test-verification.md
git commit -m "docs: add Phase 1 verification for UEP flexibility"
```

---

## Phase 2: Update writing-plans skill

**User-facing outcome:** writing-plans references UEP from design, mentions verification file will be created by execution skills

---

### Task 2.1: Update plan header template in writing-plans

**What changes after this task:** Plan header is UEP-agnostic

**Files:**
- Modify: `skills/writing-plans/SKILL.md:56-78`

**Step 1: Locate plan header template**

Find the "Plan Document Header" section.

**Step 2: Verify header already has UEP fields**

The current header already has:
- `**User Entrypoint:** [From design doc - e.g., "Web application"]`
- `**Verification Method:** [e.g., "Playwright browser testing - each phase verified through UI"]`

These are good. Update the examples to be more inclusive:

```markdown
**User Entrypoint:** [From design doc - e.g., "Web Application", "CLI Tool", "Cloud Dashboard"]

**Verification Method:** [From design doc - e.g., "Playwright browser testing", "Command execution + output checking", "Terraform Cloud workspace verification"]
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/writing-plans/SKILL.md
git commit -m "feat(writing-plans): expand UEP examples in plan header"
```

---

### Task 2.2: Update User-Testable Phase Structure section

**What changes after this task:** Phase structure guidance is UEP-agnostic

**Files:**
- Modify: `skills/writing-plans/SKILL.md:31-54`

**Step 1: Locate "User-Testable Phase Structure" section**

Find the section currently titled "User-Testable Phase Structure (Web Apps)".

**Step 2: Replace with UEP-agnostic version**

```markdown
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
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/writing-plans/SKILL.md
git commit -m "feat(writing-plans): make phase structure guidance UEP-agnostic"
```

---

### Task 2.3: Update task structure template

**What changes after this task:** Task template verification step is UEP-agnostic

**Files:**
- Modify: `skills/writing-plans/SKILL.md:80-131`

**Step 1: Locate task structure template**

Find the "Task Structure" section with the markdown template.

**Step 2: Update Step 5 (Playwright verification)**

Replace the current Step 5:

```markdown
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
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/writing-plans/SKILL.md
git commit -m "feat(writing-plans): make task verification step UEP-agnostic"
```

---

### Task 2.4: Add note about verification file creation

**What changes after this task:** writing-plans mentions that execution skills create verification file

**Files:**
- Modify: `skills/writing-plans/SKILL.md:17-21`

**Step 1: Locate the "Save plans to" section**

Find lines with `**Save plans to:**` and the verification file reference.

**Step 2: Update to clarify execution skills create verification file**

Replace lines 17-21 with:

```markdown
**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>-implementation.md`

**Verification tracking:** Execution skills (executing-plans, subagent-driven-development) will create and maintain `docs/plans/YYYY-MM-DD-<feature-name>-test-verification.md` at phase completion. This plan does not create that file.

**Critical:** The last planned task in each phase MUST be complete testing of user-facing outcomes using the designated UEP Verification Method.
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/writing-plans/SKILL.md
git commit -m "feat(writing-plans): clarify verification file created by execution skills"
```

---

### Task 2.5: Phase 2 verification and test-verification.md update

**What the user can do after this task:** Phase 2 is verified and documented

**Files:**
- Append: `docs/plans/2026-02-09-uep-flexibility-test-verification.md`

**Step 1: Verify writing-plans skill changes**

Read `skills/writing-plans/SKILL.md` and verify:
- [ ] Plan header examples include multiple UEP types
- [ ] Phase structure section is UEP-agnostic with examples for Web, CLI, Cloud
- [ ] Task template verification step covers all UEP types
- [ ] Notes clarify verification file created by execution skills

**Step 2: Append Phase 2 verification**

```markdown
---

## Phase 2: Update writing-plans skill
**Verification performed:** [DATE]
**What was tested:**
- Read skills/writing-plans/SKILL.md
- Confirmed plan header has inclusive UEP examples
- Confirmed phase structure guidance covers Web, CLI, Cloud Dashboard
- Confirmed task template verification step is UEP-agnostic
- Confirmed verification file creation delegated to execution skills
**Result:** PASS/FAIL
```

**Step 3: Commit**

```bash
git add docs/plans/2026-02-09-uep-flexibility-test-verification.md
git commit -m "docs: add Phase 2 verification for UEP flexibility"
```

---

## Phase 3: Update executing-plans skill with verification file management

**User-facing outcome:** executing-plans creates/appends verification file at phase completion with semantic summary

---

### Task 3.1: Add Verification File Management section to executing-plans

**What changes after this task:** executing-plans has clear instructions for verification file creation

**Files:**
- Modify: `skills/executing-plans/SKILL.md` - Add new section after "Binary Outcomes"

**Step 1: Locate insertion point**

Find the "Binary Outcomes" section (ends around line 65) and identify where to add new section.

**Step 2: Add Verification File Management section**

Insert after Binary Outcomes section:

```markdown
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
```

**Step 3: Verify the edit**

Read the file to confirm section added correctly.

**Step 4: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "feat(executing-plans): add verification file management section"
```

---

### Task 3.2: Update verification section to be UEP-agnostic

**What changes after this task:** Verification guidance covers all UEP types, not just Playwright

**Files:**
- Modify: `skills/executing-plans/SKILL.md:67-84`

**Step 1: Locate "Playwright-Only Verification" section**

Find the section currently titled "Playwright-Only Verification (Web Apps)".

**Step 2: Replace with UEP-agnostic verification section**

```markdown
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
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "feat(executing-plans): make verification guidance UEP-agnostic"
```

---

### Task 3.3: Update Binary Outcomes section for UEP flexibility

**What changes after this task:** Binary outcomes reference UEP verification, not just Playwright

**Files:**
- Modify: `skills/executing-plans/SKILL.md:52-66`

**Step 1: Locate "Binary Outcomes" section**

Find the section currently referencing Playwright specifically.

**Step 2: Update to reference UEP verification**

```markdown
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
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "feat(executing-plans): update binary outcomes for UEP flexibility"
```

---

### Task 3.4: Update ASK template for UEP flexibility

**What changes after this task:** ASK template references UEP verification, not just Playwright

**Files:**
- Modify: `skills/executing-plans/SKILL.md:85-116`

**Step 1: Locate "Required ASK Template" section**

Find the section with the failure template.

**Step 2: Update template to be UEP-agnostic**

```markdown
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
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "feat(executing-plans): update ASK template for UEP flexibility"
```

---

### Task 3.5: Update Remember section and integration references

**What changes after this task:** Remember section and integration are UEP-agnostic

**Files:**
- Modify: `skills/executing-plans/SKILL.md:125-142`

**Step 1: Locate "Remember" section**

Find the bullet list of reminders.

**Step 2: Update to be UEP-agnostic**

```markdown
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
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "feat(executing-plans): update remember section for UEP and verification file"
```

---

### Task 3.6: Phase 3 verification and test-verification.md update

**What the user can do after this task:** Phase 3 is verified and documented

**Files:**
- Append: `docs/plans/2026-02-09-uep-flexibility-test-verification.md`

**Step 1: Verify executing-plans skill changes**

Read `skills/executing-plans/SKILL.md` and verify:
- [ ] Verification File Management section added with format
- [ ] UEP-Appropriate Verification section replaces Playwright-only
- [ ] Binary Outcomes references UEP verification
- [ ] ASK template is UEP-agnostic
- [ ] Remember section includes verification file update reminder

**Step 2: Append Phase 3 verification**

```markdown
---

## Phase 3: Update executing-plans skill
**Verification performed:** [DATE]
**What was tested:**
- Read skills/executing-plans/SKILL.md
- Confirmed Verification File Management section with create/append instructions
- Confirmed UEP-Appropriate Verification covers all 5 UEP types
- Confirmed Binary Outcomes is UEP-agnostic
- Confirmed ASK template includes UEP Type field
- Confirmed Remember section mentions verification file
**Result:** PASS/FAIL
```

**Step 3: Commit**

```bash
git add docs/plans/2026-02-09-uep-flexibility-test-verification.md
git commit -m "docs: add Phase 3 verification for UEP flexibility"
```

---

## Phase 4: Update subagent-driven-development skill

**User-facing outcome:** subagent-driven-development creates/appends verification file at phase completion, prompts reference UEP

---

### Task 4.1: Add Verification File Management to subagent-driven-development

**What changes after this task:** subagent-driven-development has verification file instructions

**Files:**
- Modify: `skills/subagent-driven-development/SKILL.md` - Add section before "Red Flags"

**Step 1: Locate insertion point**

Find "Red Flags" section (around line 204) and insert new section before it.

**Step 2: Add Verification File Management section**

```markdown
## Verification File Management

**At the completion of each phase**, the controller (you) must create or append to the verification file.

**File location:** `docs/plans/YYYY-MM-DD-<feature-name>-test-verification.md`

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
- [Evidence from E2E verification step]
**Result:** PASS
```

**This is the controller's responsibility, not the subagent's.**

```

**Step 3: Verify the edit**

Read the file to confirm section added.

**Step 4: Commit**

```bash
git add skills/subagent-driven-development/SKILL.md
git commit -m "feat(subagent-driven-dev): add verification file management section"
```

---

### Task 4.2: Update E2E verification references to be UEP-agnostic

**What changes after this task:** References to E2E/Playwright are UEP-agnostic

**Files:**
- Modify: `skills/subagent-driven-development/SKILL.md` - Multiple locations

**Step 1: Find and update E2E references**

Search for "E2E" and "Playwright" in the file and update:

- Line ~56: "Controller runs E2E verification (for UI tasks)" → "Controller runs UEP verification"
- Line ~57: "E2E passes with evidence?" → "UEP verification passes with evidence?"
- Line ~220: "Skip E2E verification for UI changes" → "Skip UEP verification"
- Line ~222: "Move to next task without E2E verification (for UI work)" → "Move to next task without UEP verification"

**Step 2: Update the process diagram**

In the dot diagram, update node labels to reference UEP instead of E2E.

**Step 3: Verify the edits**

Read the file to confirm all E2E references updated appropriately.

**Step 4: Commit**

```bash
git add skills/subagent-driven-development/SKILL.md
git commit -m "feat(subagent-driven-dev): update E2E references to UEP verification"
```

---

### Task 4.3: Update implementer prompt template for UEP awareness

**What changes after this task:** Implementer prompt references UEP verification

**Files:**
- Modify: `skills/subagent-driven-development/implementer-prompt.md:75-86`

**Step 1: Locate verification evidence section**

Find the "Verification evidence (REQUIRED)" section.

**Step 2: Update to be UEP-agnostic**

```markdown
    - **Verification evidence (REQUIRED):**
      - For unit tests: Show test command output (X/X passing)
      - For UEP verification: Show output appropriate to project's User Entrypoint:
        - Web Application: Playwright output or browser state
        - CLI Tool: Command output with exit code
        - API Service: Request/response with status code
        - Cloud Dashboard: State verification output
        - Library/SDK: Test output or example script execution
      - "I verified it" is NOT evidence - show actual output
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/subagent-driven-development/implementer-prompt.md
git commit -m "feat(subagent-driven-dev): update implementer prompt for UEP verification"
```

---

### Task 4.4: Update spec reviewer prompt for UEP awareness

**What changes after this task:** Spec reviewer checks for UEP-appropriate verification

**Files:**
- Modify: `skills/subagent-driven-development/spec-reviewer-prompt.md:58-63`

**Step 1: Locate verification evidence section**

Find the "Verification evidence" section.

**Step 2: Update to be UEP-agnostic**

```markdown
    **Verification evidence:**
    - Did they show test output (not just claim "tests pass")?
    - Did they show UEP verification evidence appropriate to project type?
      - Web: Playwright/browser evidence
      - CLI: Command output
      - API: Request/response
      - Cloud: State verification
      - Library: Test/example output
    - Is there actual proof of verification, or just claims?
```

**Step 3: Verify the edit**

Read the file to confirm.

**Step 4: Commit**

```bash
git add skills/subagent-driven-development/spec-reviewer-prompt.md
git commit -m "feat(subagent-driven-dev): update spec reviewer for UEP verification"
```

---

### Task 4.5: Phase 4 verification and test-verification.md update

**What the user can do after this task:** Phase 4 is verified and documented

**Files:**
- Append: `docs/plans/2026-02-09-uep-flexibility-test-verification.md`

**Step 1: Verify subagent-driven-development skill changes**

Read all modified files and verify:
- [ ] SKILL.md has Verification File Management section
- [ ] E2E references updated to UEP verification
- [ ] implementer-prompt.md verification evidence is UEP-agnostic
- [ ] spec-reviewer-prompt.md checks for UEP-appropriate evidence

**Step 2: Append Phase 4 verification**

```markdown
---

## Phase 4: Update subagent-driven-development skill
**Verification performed:** [DATE]
**What was tested:**
- Read skills/subagent-driven-development/SKILL.md
- Confirmed Verification File Management section added
- Confirmed E2E references changed to UEP verification
- Read implementer-prompt.md, confirmed UEP-agnostic verification evidence
- Read spec-reviewer-prompt.md, confirmed UEP-appropriate evidence checks
**Result:** PASS/FAIL
```

**Step 3: Commit**

```bash
git add docs/plans/2026-02-09-uep-flexibility-test-verification.md
git commit -m "docs: add Phase 4 verification for UEP flexibility"
```

---

## Phase 5: Final verification and cleanup

**User-facing outcome:** All changes verified, documentation complete

---

### Task 5.1: Cross-skill consistency check

**What changes after this task:** All skills use consistent terminology

**Files:**
- Read: All 4 modified skill files

**Step 1: Verify terminology consistency**

Check that all files use:
- "User Entrypoint" or "UEP" consistently
- Same 6 UEP types listed in same order
- "Verification Method" (not "verification approach" or other variants)

**Step 2: Fix any inconsistencies found**

Make edits as needed.

**Step 3: Commit if changes made**

```bash
git add skills/
git commit -m "fix: ensure consistent UEP terminology across skills"
```

---

### Task 5.2: Final verification and test-verification.md completion

**What the user can do after this task:** Implementation is complete and verified

**Files:**
- Append: `docs/plans/2026-02-09-uep-flexibility-test-verification.md`

**Step 1: Final verification checklist**

- [ ] brainstorming: Checks CLAUDE.md, infers, offers 6 options, persists
- [ ] writing-plans: UEP-agnostic examples, delegates verification file to execution
- [ ] executing-plans: Verification file management, UEP-appropriate verification
- [ ] subagent-driven-development: Same verification file management, UEP prompts

**Step 2: Append final verification**

```markdown
---

## Phase 5: Final verification and cleanup
**Verification performed:** [DATE]
**What was tested:**
- Cross-checked all 4 skills for terminology consistency
- Verified UEP types listed consistently
- Verified verification file format consistent between executing-plans and subagent-driven-development
**Result:** PASS/FAIL

---

## Summary
All phases completed. Skills now support flexible User Entrypoint discovery and verification tracking.
```

**Step 3: Commit**

```bash
git add docs/plans/2026-02-09-uep-flexibility-test-verification.md
git commit -m "docs: complete UEP flexibility verification"
```
