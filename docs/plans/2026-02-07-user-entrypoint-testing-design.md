# User-Entrypoint Testing Design

## Overview

Every implementation phase must be verified from the user's perspective, not just the developer's. For web apps, this means Playwright browser testing through the actual UI — no backend-only phases, no unit-test-only verification, no workarounds.

## Core Philosophy

A phase is only successful if:
1. The user can interact with the result through their primary entrypoint (browser for web apps)
2. Playwright testing confirms it works
3. The outcome is binary: PASS or ASK (no blocked, skipped, or partial success)

## Scope

**In scope:**
- Web applications with browser-based UI
- Playwright as the sole verification method
- The three main workflow skills: brainstorming, writing-plans, executing-plans

**Out of scope (for now):**
- CLI tools, libraries, APIs without frontends
- Mobile applications
- Alternative testing tools (Cypress, etc.)

**Non-web-apps:** Warn and fall back to best-effort verification.

---

## Changes to Brainstorming

### 1. Detect and Confirm Entrypoint Type

Before design work begins, analyze the project to infer entrypoint type:
- Has `index.html`, React/Vue/Svelte, or web framework? → Web app
- Has both frontend and backend directories? → Full-stack web app
- No frontend? → Warn and fall back

**Confirmation prompt:**
> "This appears to be a **web application**. All implementation phases will be verified via Playwright browser testing — each phase must leave the app in a working state that can be tested through the UI. Is that correct?"

### 2. Ask "How Will Users Interact?"

For any feature request, proactively surface the user interaction question:
> "How will users interact with [feature]?"

With options like: UI (forms, buttons, pages), API-only, CLI, etc.

### 3. Pitch Missing UI Components

If the user requests functionality without mentioning UI, propose what's needed:

> "For users to actually use these CRUD operations, you'd likely need:
> - A list/table view to see existing items
> - A create form (modal or page?)
> - Edit functionality (inline or separate form?)
> - Delete confirmation
>
> Should I include these in the design?"

### 4. Confirm Complete User-Facing Scope

Before proceeding to design, summarize what will be built from the user's perspective:

> "So when this is done, a user will be able to: [list of user-facing capabilities]. All phases will be verified by testing these through the browser. Correct?"

**Store in design doc:** The entrypoint type and user-facing capabilities that each phase must enable.

---

## Changes to Writing Plans

### 1. Reference Entrypoint from Design

The plan header must reference the entrypoint type from the design doc and confirm that all phases will be verified accordingly.

### 2. Structure Phases as User-Testable Units

Every phase must leave the app in a state where the user can test something through the UI.

**Instead of:**
```
Phase 1: Set up database schema for recipes
Phase 2: Add API endpoints for recipes
Phase 3: Build recipe list UI
```

**It becomes:**
```
Phase 1: User can see an empty recipe list
  (schema + API + basic list UI)
Phase 2: User can add a recipe via form
  (create endpoint + form UI)
Phase 3: User can delete a recipe
  (delete endpoint + delete button + confirmation)
```

### 3. Bundle Infrastructure with First User-Visible Feature

Infrastructure work cannot be its own phase. It must be bundled with the first feature that makes it user-visible.

### 4. Each Phase Declares Playwright Verification

Every phase must specify:
- What the user will be able to do after this phase
- The Playwright test that proves it works
- The expected UI state after the test

```
Phase 1: User can see empty recipe list
- Verification: Playwright navigates to /recipes, confirms "No recipes yet" message
- Pass criteria: Message visible, no console errors
```

---

## Changes to Executing Plans

### 1. Playwright-Only Verification (Web Apps)

For web applications, all phase verification must use Playwright browser testing. No fallbacks allowed:
- No curl or API testing
- No unit tests as final verification
- No "I checked the code and it looks right"

### 2. Binary PASS or ASK Outcomes

Each phase ends in one of two states:

**PASS:** Playwright test passes → commit, move to next phase

**ASK:** Playwright test fails → stop and request help using required template

No other outcomes are valid:
- No "blocked"
- No "skipped"
- No "partial success"
- No "tests failed but continuing anyway"

### 3. Required ASK Template

When a phase cannot pass, the agent must use this format:

```markdown
## Phase Failed: [Phase Name]

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

### 4. No Workarounds Allowed

The agent cannot:
- Skip the phase and continue
- Mark it as "blocked" and move on
- Verify via unit tests or API calls instead
- Assume it works without Playwright confirmation

---

## Integration Flow

```
BRAINSTORMING                    WRITING-PLANS                 EXECUTING-PLANS
     │                                │                              │
     ▼                                ▼                              │
Detect entrypoint ──────────────► Referenced in plan header         │
     │                                │                              │
     ▼                                ▼                              │
Pitch missing UI ───────────────► Each phase includes UI work       │
     │                                │                              │
     ▼                                ▼                              ▼
"User will be able to..." ──────► Phase verification spec ────► Playwright test
     │                                │                              │
     ▼                                ▼                              ▼
Design doc saved ───────────────► Plan doc references design ──► Binary PASS/ASK
```

---

## Summary

| Skill | New Requirements |
|-------|------------------|
| **Brainstorming** | 1. Detect/confirm entrypoint type<br>2. Ask "how will users interact?"<br>3. Pitch missing UI components<br>4. Confirm complete user-facing scope |
| **Writing-plans** | 1. Reference entrypoint from design<br>2. Structure phases as user-testable units<br>3. Bundle infra with first UI use<br>4. Each phase declares Playwright verification |
| **Executing-plans** | 1. Enforce Playwright-only verification<br>2. Binary PASS/ASK outcomes<br>3. Required ASK template with evidence<br>4. No skip/blocked/workaround allowed |
