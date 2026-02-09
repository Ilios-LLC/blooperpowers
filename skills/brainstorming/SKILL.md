---
name: brainstorming
description: "You MUST use this before any creative work - creating features, building components, adding functionality, or modifying behavior. Explores user intent, requirements and design before implementation."
---

# Brainstorming Ideas Into Designs

## Overview

Help turn ideas into fully formed designs and specs through natural collaborative dialogue.

Start by understanding the current project context, then ask questions one at a time to refine the idea. Once you understand what you're building, present the design in small sections (200-300 words), checking after each section whether it looks right so far.

## The Process

**Understanding the idea:**
- Check out the current project state first (files, docs, recent commits)
- Ask questions one at a time to refine the idea
- Prefer multiple choice questions when possible, but open-ended is fine too
- Only one question per message - if a topic needs more exploration, break it into multiple questions
- Focus on understanding: purpose, constraints, success criteria

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

- Store UEP type and Verification Method in design document for use by writing-plans

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

**Exploring approaches:**
- Propose 2-3 different approaches with trade-offs
- Present options conversationally with your recommendation and reasoning
- Lead with your recommended option and explain why

**Presenting the design:**
- Once you believe you understand what you're building, present the design
- Break it into sections of 200-300 words
- Ask after each section whether it looks right so far
- Cover: architecture, components, data flow, error handling, testing
- Be ready to go back and clarify if something doesn't make sense

## After the Design

**Documentation:**
- Write the validated design to `docs/plans/YYYY-MM-DD-<topic>-design.md`
- Design document MUST include:
  - **User Entrypoint type:** (e.g., "CLI Tool", "Web Application", "Cloud Dashboard")
  - **Verification Method:** How phases will be verified (e.g., "Command execution + output checking")
  - **User-facing capabilities:** List of what users can do when complete
  - **Phase verification approach:** How each phase will be tested from user perspective
- Use elements-of-style:writing-clearly-and-concisely skill if available
- Commit the design document to git

**Implementation (if continuing):**
- Ask: "Ready to set up for implementation?"
- Use blooperpowers:writing-plans to create detailed implementation plan

## Key Principles

- **One question at a time** - Don't overwhelm with multiple questions
- **Multiple choice preferred** - Easier to answer than open-ended when possible
- **YAGNI ruthlessly** - Remove unnecessary features from all designs
- **Explore alternatives** - Always propose 2-3 approaches before settling
- **Incremental validation** - Present design in sections, validate each
- **Be flexible** - Go back and clarify when something doesn't make sense
