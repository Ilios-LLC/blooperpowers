# User-Entrypoint Testing Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Update the brainstorming, writing-plans, and executing-plans skills to enforce user-entrypoint testing for all implementation phases.

**Architecture:** Modify three SKILL.md files to add new sections and requirements. Each skill builds on the previous: brainstorming captures entrypoint info, writing-plans uses it to structure phases, executing-plans enforces it during verification.

**Tech Stack:** Markdown skill files

**Design Reference:** `docs/plans/2026-02-07-user-entrypoint-testing-design.md`

---

## Phase 1: Update Brainstorming Skill

### Task 1: Add User Entrypoint Detection Section

**Files:**
- Modify: `skills/brainstorming/SKILL.md:16-21`

**What user can do after this task:** When an agent uses the brainstorming skill, it will detect and confirm the project's user entrypoint type before designing.

**Step 1: Add new section after "Understanding the idea"**

Insert this new section between "Understanding the idea" and "Exploring approaches":

```markdown
**Identifying user entrypoint (REQUIRED for web apps):**
- Analyze project structure to infer entrypoint type:
  - Has `index.html`, React/Vue/Svelte, or web framework? → Web app
  - Has both frontend and backend directories? → Full-stack web app
  - No frontend detected? → Warn and use best-effort verification
- Confirm with user:
  > "This appears to be a **web application**. All implementation phases will be verified via Playwright browser testing — each phase must leave the app in a working state that can be tested through the UI. Is that correct?"
- Store entrypoint type in design document for use by writing-plans
```

**Step 2: Verify the change**

Read the file and confirm the new section appears between lines 16-30.

---

### Task 2: Add User Interaction Discovery Section

**Files:**
- Modify: `skills/brainstorming/SKILL.md` (after the section added in Task 1)

**What user can do after this task:** When brainstorming features, the agent will proactively ask how users will interact with the feature and pitch missing UI components.

**Step 1: Add new section after "Identifying user entrypoint"**

```markdown
**Discovering user interactions (REQUIRED):**
- For every feature request, ask: "How will users interact with [feature]?"
  - Options: UI (forms, buttons, pages), API-only, CLI, etc.
- If user mentions functionality without specifying UI, pitch missing components:
  > "For users to actually use [feature], you'd likely need:
  > - [List specific UI components: forms, lists, buttons, etc.]
  >
  > Should I include these in the design?"
- Before proceeding to design, confirm complete user-facing scope:
  > "So when this is done, a user will be able to: [list capabilities]. All phases will be verified by testing these through the browser. Correct?"
```

**Step 2: Verify the change**

Read the file and confirm the new section appears after "Identifying user entrypoint".

---

### Task 3: Update Design Documentation Requirements

**Files:**
- Modify: `skills/brainstorming/SKILL.md:35-40` (After the Design section)

**What user can do after this task:** Design documents will include entrypoint type and user-facing capabilities for downstream skills to reference.

**Step 1: Update the Documentation section**

Replace the existing Documentation section with:

```markdown
**Documentation:**
- Write the validated design to `docs/plans/YYYY-MM-DD-<topic>-design.md`
- Design document MUST include:
  - **Entrypoint type:** (e.g., "Web application - Playwright verification")
  - **User-facing capabilities:** List of what users can do when complete
  - **Phase verification approach:** How each phase will be tested from user perspective
- Use elements-of-style:writing-clearly-and-concisely skill if available
- Commit the design document to git
```

**Step 2: Verify the change**

Read the file and confirm the Documentation section includes the new requirements.

---

## Phase 2: Update Writing-Plans Skill

### Task 4: Add Entrypoint Reference in Plan Header

**Files:**
- Modify: `skills/writing-plans/SKILL.md:29-45`

**What user can do after this task:** Every implementation plan will reference the entrypoint type and declare how phases will be verified.

**Step 1: Update the Plan Document Header template**

Replace the existing header template with:

```markdown
**Every plan MUST start with this header:**

\`\`\`markdown
# [Feature Name] Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

**User Entrypoint:** [From design doc - e.g., "Web application"]

**Verification Method:** [e.g., "Playwright browser testing - each phase verified through UI"]

**User-Facing Capabilities:** [List from design doc]

---
\`\`\`
```

**Step 2: Verify the change**

Read the file and confirm the header template includes User Entrypoint, Verification Method, and User-Facing Capabilities.

---

### Task 5: Add User-Testable Phase Structure Section

**Files:**
- Modify: `skills/writing-plans/SKILL.md` (new section after Bite-Sized Task Granularity)

**What user can do after this task:** Plans will structure phases as vertical slices that are each user-testable.

**Step 1: Add new section after "Bite-Sized Task Granularity"**

```markdown
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
```

**Step 2: Verify the change**

Read the file and confirm the new section appears after "Bite-Sized Task Granularity".

---

### Task 6: Add Playwright Verification Step to Task Structure

**Files:**
- Modify: `skills/writing-plans/SKILL.md:47-88` (Task Structure section)

**What user can do after this task:** Each task in a plan will include explicit Playwright verification with expected outcomes.

**Step 1: Update the Task Structure template**

Replace the existing template to add Playwright verification:

```markdown
## Task Structure

\`\`\`markdown
### Task N: [User-Facing Outcome]

**What the user can do after this task:** [e.g., "User can see an empty recipe list"]

**Files:**
- Create: `exact/path/to/file.py`
- Modify: `exact/path/to/existing.py:123-145`
- Test: `tests/exact/path/to/test.py`

**Step 1: Write the failing test**

\`\`\`python
def test_specific_behavior():
    result = function(input)
    assert result == expected
\`\`\`

**Step 2: Run test to verify it fails**

Run: `pytest tests/path/test.py::test_name -v`
Expected: FAIL with "function not defined"

**Step 3: Write minimal implementation**

\`\`\`python
def function(input):
    return expected
\`\`\`

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

\`\`\`bash
git add tests/path/test.py src/path/file.py
git commit -m "feat: add specific feature"
\`\`\`
\`\`\`
```

**Step 2: Verify the change**

Read the file and confirm Step 5 (Playwright verification) is included in the task template.

---

## Phase 3: Update Executing-Plans Skill

### Task 7: Add Binary Outcome Enforcement Section

**Files:**
- Modify: `skills/executing-plans/SKILL.md` (new section after "The Process")

**What user can do after this task:** The executing-plans skill will enforce binary PASS/ASK outcomes with no exceptions.

**Step 1: Add new section after "The Process"**

```markdown
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
```

**Step 2: Verify the change**

Read the file and confirm the Binary Outcomes section is present.

---

### Task 8: Add Required ASK Template

**Files:**
- Modify: `skills/executing-plans/SKILL.md` (replace "When to Stop and Ask for Help" section)

**What user can do after this task:** When a task fails, the agent will use a structured template with evidence.

**Step 1: Replace the existing "When to Stop and Ask for Help" section**

```markdown
## Required ASK Template

**When Playwright verification fails, use this exact format:**

\`\`\`markdown
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
\`\`\`

**You must provide:**
- Specific failure description (not "it didn't work")
- Evidence from Playwright (output, screenshots, console)
- What you already tried
- A specific, actionable ask
```

**Step 2: Verify the change**

Read the file and confirm the ASK template is present with all required sections.

---

### Task 9: Add Playwright-Only Verification Requirement

**Files:**
- Modify: `skills/executing-plans/SKILL.md` (new section after Binary Outcomes)

**What user can do after this task:** The skill will explicitly require Playwright for web app verification.

**Step 1: Add new section**

```markdown
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
```

**Step 2: Verify the change**

Read the file and confirm the Playwright-Only section is present.

---

### Task 10: Update Remember Section

**Files:**
- Modify: `skills/executing-plans/SKILL.md:70-77` (Remember section)

**What user can do after this task:** The Remember section will reinforce the new requirements.

**Step 1: Replace the existing Remember section**

```markdown
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
```

**Step 2: Verify the change**

Read the file and confirm the Remember section includes the new bullet points.

---

## Phase 4: Verification

### Task 11: Final Review of All Three Skills

**What user can do after this task:** Confirm all changes are complete and consistent across the three skills.

**Step 1: Read and verify brainstorming/SKILL.md**

Confirm it includes:
- Identifying user entrypoint section
- Discovering user interactions section
- Updated Documentation requirements

**Step 2: Read and verify writing-plans/SKILL.md**

Confirm it includes:
- Updated header with User Entrypoint, Verification Method, User-Facing Capabilities
- User-Testable Phase Structure section
- Playwright verification step in task template

**Step 3: Read and verify executing-plans/SKILL.md**

Confirm it includes:
- Binary Outcomes section
- Required ASK Template
- Playwright-Only Verification section
- Updated Remember section

---
