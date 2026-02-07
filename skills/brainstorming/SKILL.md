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

**Identifying user entrypoint (REQUIRED for web apps):**
- Analyze project structure to infer entrypoint type:
  - Has `index.html`, React/Vue/Svelte, or web framework? → Web app
  - Has both frontend and backend directories? → Full-stack web app
  - No frontend detected? → Warn and use best-effort verification
- Confirm with user:
  > "This appears to be a **web application**. All implementation phases will be verified via Playwright browser testing — each phase must leave the app in a working state that can be tested through the UI. Is that correct?"
- Store entrypoint type in design document for use by writing-plans

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
  - **Entrypoint type:** (e.g., "Web application - Playwright verification")
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
