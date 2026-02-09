# User Entrypoint (UEP) Flexibility Design

> **Entrypoint type:** Library/SDK (this is a skills plugin repository)
> **User-facing capabilities:** Skills that adapt to any project's verification approach
> **Phase verification approach:** Unit tests + manual skill invocation testing

## Problem Statement

Current skills assume web applications with Playwright verification. This doesn't serve:
- CLI tools (verify via command execution)
- API services (verify via endpoint testing)
- Infrastructure/IaC projects (verify via cloud dashboards)
- Libraries (verify via tests + example consumers)

## Solution Overview

1. **UEP Discovery** - Check project's CLAUDE.md for stored UEP, infer from codebase if missing, ask user to confirm
2. **UEP Storage** - Structured block in project's CLAUDE.md
3. **UEP Types** - 6 curated types with "Other" for edge cases
4. **Verification Tracking** - Execution skills create/append to `*-test-verification.md` at phase completion

---

## UEP Storage Format

In a project's CLAUDE.md:

```markdown
## User Entrypoint
Type: Web Application
Verification: Playwright browser testing at http://localhost:3000
```

Or for other types:

```markdown
## User Entrypoint
Type: CLI Tool
Verification: Command execution with output checking
```

```markdown
## User Entrypoint
Type: Cloud Dashboard
Verification: Terraform Cloud workspace my-org/production
```

---

## UEP Discovery Flow

1. **Check CLAUDE.md** - Parse for `## User Entrypoint` section
2. **If found** - Confirm with user: "I see your UEP is [Type] with [Verification]. Correct?"
3. **If not found** - Explore codebase to infer type:
   - Has `package.json` with React/Vue/Svelte/Next → Web Application
   - Has `main.go` or `cmd/` directory → CLI Tool
   - Has `terraform/` or `.tf` files → Cloud Dashboard (Terraform)
   - Has `openapi.yaml` or API routes only → API Service
   - Has `setup.py`/`pyproject.toml` with library structure → Library/SDK
4. **Present inference**: "This looks like a **[Type]** based on [evidence]. Is that correct?"
5. **If incorrect**, offer options:
   - Web Application
   - CLI Tool
   - API Service
   - Cloud Dashboard (Terraform, Vercel, AWS, etc.)
   - Library/SDK
   - Other (describe)
6. **Offer to persist**: "Would you like me to add this to your CLAUDE.md so future sessions remember it?"

---

## UEP Types and Verification Guidance

### Web Application
- **Verification:** Playwright browser testing
- **Example:** Navigate to page, fill form, click submit, verify success message
- **Evidence:** Playwright assertions passing, screenshot if useful

### CLI Tool
- **Verification:** Command execution with output checking
- **Example:** Run `mytool --help`, verify expected flags; run `mytool create foo`, verify "Created foo" output
- **Evidence:** Command + actual output shown in conversation

### API Service
- **Verification:** Endpoint testing via test client or curl
- **Example:** `curl -X POST /api/users -d '{"name":"test"}'` returns 201 with user ID
- **Evidence:** Request + response shown in conversation

### Cloud Dashboard
- **Verification:** Manual or API check of cloud platform state
- **Example:** Verify Terraform Cloud shows successful apply; check Vercel deployment is live
- **Evidence:** Description of what was checked, or API response if available

### Library/SDK
- **Verification:** Unit tests + example consumer code
- **Example:** Tests pass; example script using the library produces expected output
- **Evidence:** Test output + example script execution

### Other (free-form)
- **Verification:** As described by user
- **Example:** "Check Datadog dashboard shows new metric", "Verify Slack webhook fires"
- **Evidence:** Description of verification performed and result observed

---

## Verification File Format

**Location:** `docs/plans/YYYY-MM-DD-<feature-name>-test-verification.md`

**Created by:** executing-plans or subagent-driven-development (not writing-plans)

**When:** At completion of each phase

```markdown
# <Feature Name> Test Verification

> UEP Type: CLI Tool
> Verification Method: Command execution + output verification

---

## Phase 1: Basic Authentication
**Verification performed:** 2026-02-09
**What was tested:**
- Ran `myapp login --user test@example.com`
- Confirmed output: "Login successful, token saved to ~/.myapp/credentials"
- Ran `myapp whoami` to verify session persists
**Result:** PASS

---

## Phase 2: Token Refresh
**Verification performed:** 2026-02-09
**What was tested:**
- Expired token by setting system clock forward
- Ran `myapp status` - confirmed automatic refresh occurred
- Verified new token in credentials file
**Result:** PASS
```

---

## Skills to Modify

### 1. brainstorming/SKILL.md
- Update "Identifying user entrypoint" section:
  - First check CLAUDE.md for existing `## User Entrypoint` block
  - If not found, explore codebase and infer type
  - Present the 6 UEP options
  - Offer to add UEP block to project's CLAUDE.md
- Remove web-app-centric language ("REQUIRED for web apps" → "REQUIRED for all projects")
- Add verification guidance for each UEP type

### 2. writing-plans/SKILL.md
- Update plan header template to include UEP Type and Verification Method
- Add note: "Execution skills will create `docs/plans/YYYY-MM-DD-<feature-name>-test-verification.md` at phase completion"
- Remove any references to creating the verification file itself
- Update phase verification language to be UEP-agnostic

### 3. executing-plans/SKILL.md
- Add "Verification File Management" section
- At phase completion: check if `*-test-verification.md` exists, create if not, append phase summary
- Update verification examples to cover multiple UEP types

### 4. subagent-driven-development/SKILL.md
- Same verification file management as executing-plans
- Update implementer prompts to reference project's UEP type
- Update reviewer criteria to verify UEP-appropriate testing was done

---

## Edge Cases

| Case | Handling |
|------|----------|
| No CLAUDE.md exists | Infer UEP and ask user, can't persist |
| User declines to add to CLAUDE.md | Use discovered UEP for current session only |
| Multiple UEPs (CLI + Web) | Pick primary one; could extend later |
| UEP changes mid-project | User updates CLAUDE.md manually |
| Verification file already exists | Append new phases, don't overwrite |

---

## Success Criteria

1. Skills no longer assume Playwright/web app verification
2. Any project type can define its UEP in CLAUDE.md
3. Verification tracking happens consistently across execution skills
4. Verification summaries are semantic and reproducible
