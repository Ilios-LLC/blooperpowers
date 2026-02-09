# Checkpoint Markers Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use blooperpowers:executing-plans to implement this plan task-by-task.

**Goal:** Add `[CHECKPOINT]` marker convention so execution skills pause at phase boundaries for user review.

**Architecture:** Modify three skill files: writing-plans (adds markers to plans), executing-plans (detects markers and pauses), subagent-driven-development (detects markers and pauses). No new files needed.

**Tech Stack:** Markdown skill files

**User Entrypoint:** Documentation review (skill markdown)

**Verification Method:** Review skill markdown for correct instructions

**User-Facing Capabilities:**
- Write plans where `[CHECKPOINT]` marks phase boundaries
- Execute plans (either skill) with automatic pauses at phase boundaries
- Get consistent pause behavior across both execution skills
- Use legacy plans (no markers) with fallback batch-of-3 behavior

---

## Phase 1: Update writing-plans Skill

Add the `[CHECKPOINT]` convention to the plan-writing skill so new plans include markers at phase boundaries.

---

### Task 1: Add checkpoint explanation to writing-plans header section

**What the user can do after this task:** Understand what `[CHECKPOINT]` markers mean when reading the skill

**Files:**
- Modify: `skills/writing-plans/SKILL.md:20-23`

**Step 1: Add checkpoint explanation after the "Critical" line**

Find line 22-23:
```markdown
**Critical:** The last planned task in each phase MUST be complete testing of user-facing outcomes using the designated UEP Verification Method.
```

Replace with:
```markdown
**Critical:** The last planned task in each phase MUST be complete testing of user-facing outcomes using the designated UEP Verification Method. Mark these tasks with `[CHECKPOINT]` in the title.

**Checkpoint markers:** Tasks with `[CHECKPOINT]` in the title signal phase boundaries. Execution skills pause after these tasks for user review. The `[CHECKPOINT]` task must include UEP verification and update the test-verification file.
```

**Step 2: Verify the change**

- Read `skills/writing-plans/SKILL.md` lines 20-30
- Confirm both paragraphs are present and explain the checkpoint convention

**Step 3: Commit**

```bash
git add skills/writing-plans/SKILL.md
git commit -m "docs(writing-plans): Add checkpoint marker explanation"
```

---

### Task 2: Update task structure template to show checkpoint format

**What the user can do after this task:** See the correct format for checkpoint tasks in the template

**Files:**
- Modify: `skills/writing-plans/SKILL.md:89-93`

**Step 1: Update the task structure heading**

Find lines 91-92:
```markdown
```markdown
### Task N: [User-Facing Outcome]
```

Replace with:
```markdown
```markdown
### Task N: [User-Facing Outcome]

For phase-ending tasks, use:

### Task N: [CHECKPOINT] [User-Facing Outcome]
```

**Step 2: Verify the change**

- Read `skills/writing-plans/SKILL.md` lines 89-100
- Confirm both regular and checkpoint task formats are shown

**Step 3: Commit**

```bash
git add skills/writing-plans/SKILL.md
git commit -m "docs(writing-plans): Add checkpoint task format to template"
```

---

### Task 3: [CHECKPOINT] Verify writing-plans skill is complete

**What the user can do after this task:** Confirm writing-plans skill correctly documents the checkpoint convention

**Files:**
- Review: `skills/writing-plans/SKILL.md`

**Step 1: Full skill review**

Read entire `skills/writing-plans/SKILL.md` and verify:
- [ ] Checkpoint explanation exists near line 22-25
- [ ] Task template shows both regular and `[CHECKPOINT]` formats
- [ ] No conflicting instructions elsewhere in the file

**Step 2: Update test-verification file**

Create or append to `docs/plans/2026-02-09-checkpoint-markers-test-verification.md`:

```markdown
# Checkpoint Markers Test Verification

> UEP Type: Documentation review
> Verification Method: Review skill markdown for correct instructions

---

## Phase 1: Update writing-plans Skill
**Verification performed:** [Today's date]
**What was tested:**
- Read skills/writing-plans/SKILL.md
- Confirmed checkpoint explanation present at lines 22-25
- Confirmed task template shows [CHECKPOINT] format
- No conflicting instructions found
**Result:** PASS
```

**Step 3: Commit verification file**

```bash
git add docs/plans/2026-02-09-checkpoint-markers-test-verification.md
git commit -m "docs: Add phase 1 verification for checkpoint markers"
```

---

## Phase 2: Update executing-plans Skill

Replace batch-of-3 execution with checkpoint-based pauses.

---

### Task 4: Update Step 2 to use checkpoint-based batching

**What the user can do after this task:** See that executing-plans uses checkpoints instead of fixed batch sizes

**Files:**
- Modify: `skills/executing-plans/SKILL.md:24-31`

**Step 1: Replace Step 2 content**

Find lines 24-31:
```markdown
### Step 2: Execute Batch
**Default: First 3 tasks**

For each task:
1. Mark as in_progress
2. Follow each step exactly (plan has bite-sized steps)
3. Run verifications as specified
4. Mark as completed
```

Replace with:
```markdown
### Step 2: Execute Until Checkpoint
**Execute tasks until completing a `[CHECKPOINT]` task, then pause.**

For each task:
1. Mark as in_progress
2. Follow each step exactly (plan has bite-sized steps)
3. Run verifications as specified
4. Mark as completed
5. If task has `[CHECKPOINT]` in title: stop and go to Step 3

**Fallback:** If plan has no `[CHECKPOINT]` markers (legacy plans), batch by 3 tasks.
```

**Step 2: Verify the change**

- Read `skills/executing-plans/SKILL.md` lines 24-36
- Confirm checkpoint-based batching is documented with fallback

**Step 3: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "docs(executing-plans): Use checkpoint-based batching"
```

---

### Task 5: Update Step 3 to clarify checkpoint context

**What the user can do after this task:** See that reporting happens after checkpoint tasks

**Files:**
- Modify: `skills/executing-plans/SKILL.md:33-37` (line numbers will shift after Task 4)

**Step 1: Update Step 3 content**

Find the Step 3 section (after Step 2):
```markdown
### Step 3: Report
When batch complete:
- Show what was implemented
- Show verification output
- Say: "Ready for feedback."
```

Replace with:
```markdown
### Step 3: Report at Checkpoint
When `[CHECKPOINT]` task complete (or batch of 3 for legacy plans):
- Show tasks completed since last checkpoint
- Show verification output and test-verification file update
- Say: "Phase complete. Ready for feedback."
```

**Step 2: Verify the change**

- Read `skills/executing-plans/SKILL.md` Step 3 section
- Confirm it references checkpoints and shows updated messaging

**Step 3: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "docs(executing-plans): Update Step 3 for checkpoint reporting"
```

---

### Task 6: Update Remember section with checkpoint bullet

**What the user can do after this task:** See checkpoint detection in the quick-reference list

**Files:**
- Modify: `skills/executing-plans/SKILL.md:177-187`

**Step 1: Add checkpoint bullet to Remember section**

Find the Remember section (around line 177):
```markdown
## Remember
- Review plan critically first
- Follow plan steps exactly
```

Add after "Follow plan steps exactly":
```markdown
- **Pause at `[CHECKPOINT]` tasks** — these mark phase boundaries
```

**Step 2: Verify the change**

- Read `skills/executing-plans/SKILL.md` Remember section
- Confirm checkpoint bullet is present

**Step 3: Commit**

```bash
git add skills/executing-plans/SKILL.md
git commit -m "docs(executing-plans): Add checkpoint reminder"
```

---

### Task 7: [CHECKPOINT] Verify executing-plans skill is complete

**What the user can do after this task:** Confirm executing-plans skill correctly implements checkpoint pauses

**Files:**
- Review: `skills/executing-plans/SKILL.md`

**Step 1: Full skill review**

Read entire `skills/executing-plans/SKILL.md` and verify:
- [ ] Step 2 documents checkpoint-based execution with fallback
- [ ] Step 3 mentions checkpoint reporting with "Phase complete" message
- [ ] Remember section includes checkpoint bullet
- [ ] No conflicting references to "batch of 3" without fallback context

**Step 2: Update test-verification file**

Append to `docs/plans/2026-02-09-checkpoint-markers-test-verification.md`:

```markdown
## Phase 2: Update executing-plans Skill
**Verification performed:** [Today's date]
**What was tested:**
- Read skills/executing-plans/SKILL.md
- Confirmed Step 2 uses checkpoint-based batching with fallback
- Confirmed Step 3 reports at checkpoints with "Phase complete" message
- Confirmed Remember section includes checkpoint bullet
- No conflicting batch-of-3 references without fallback context
**Result:** PASS
```

**Step 3: Commit verification file**

```bash
git add docs/plans/2026-02-09-checkpoint-markers-test-verification.md
git commit -m "docs: Add phase 2 verification for checkpoint markers"
```

---

## Phase 3: Update subagent-driven-development Skill

Add checkpoint detection and pauses to subagent-driven-development.

---

### Task 8: Update the flowchart to include checkpoint pause

**What the user can do after this task:** See the checkpoint pause logic in the process diagram

**Files:**
- Modify: `skills/subagent-driven-development/SKILL.md:40-88`

**Step 1: Update the flowchart**

Find lines 83-86 in the flowchart:
```markdown
    "Mark task complete in TodoWrite" -> "More tasks remain?";
    "More tasks remain?" -> "Dispatch implementer subagent (./implementer-prompt.md)" [label="yes"];
    "More tasks remain?" -> "Dispatch final code reviewer subagent for entire implementation" [label="no"];
    "Dispatch final code reviewer subagent for entire implementation" -> "Use superpowers:finishing-a-development-branch";
```

Replace with:
```markdown
    "Mark task complete in TodoWrite" -> "Was task a [CHECKPOINT]?";
    "Was task a [CHECKPOINT]?" -> "Report and wait for feedback" [label="yes"];
    "Was task a [CHECKPOINT]?" -> "More tasks remain?" [label="no"];
    "Report and wait for feedback" -> "More tasks remain?";
    "More tasks remain?" -> "Dispatch implementer subagent (./implementer-prompt.md)" [label="yes"];
    "More tasks remain?" -> "Dispatch final code reviewer subagent for entire implementation" [label="no"];
    "Dispatch final code reviewer subagent for entire implementation" -> "Use superpowers:finishing-a-development-branch";
```

**Step 2: Verify the change**

- Read `skills/subagent-driven-development/SKILL.md` lines 80-95
- Confirm checkpoint decision node is in flowchart

**Step 3: Commit**

```bash
git add skills/subagent-driven-development/SKILL.md
git commit -m "docs(subagent-driven-development): Add checkpoint pause to flowchart"
```

---

### Task 9: Add Checkpoint Pauses section

**What the user can do after this task:** See detailed checkpoint pause instructions

**Files:**
- Modify: `skills/subagent-driven-development/SKILL.md` (after Prompt Templates section, around line 95)

**Step 1: Add new section after Prompt Templates**

Find lines 90-95:
```markdown
## Prompt Templates

- `./implementer-prompt.md` - Dispatch implementer subagent
- `./spec-reviewer-prompt.md` - Dispatch spec compliance reviewer subagent
- `./code-quality-reviewer-prompt.md` - Dispatch code quality reviewer subagent
```

Add after this section:
```markdown

## Checkpoint Pauses

**After completing any task with `[CHECKPOINT]` in the title, pause for user feedback.**

**Detection:** Check if task subject contains `[CHECKPOINT]` (case-sensitive string match)

**When checkpoint reached:**
1. Report tasks completed since last checkpoint
2. Show verification file update (what was written to test-verification.md)
3. Say: "Phase complete. Ready for feedback."
4. Wait for user response before continuing

**Fallback:** If plan has no `[CHECKPOINT]` markers (legacy plans), execute all tasks without intermediate pauses (original behavior).
```

**Step 2: Verify the change**

- Read `skills/subagent-driven-development/SKILL.md` around line 95-115
- Confirm Checkpoint Pauses section exists with detection and reporting instructions

**Step 3: Commit**

```bash
git add skills/subagent-driven-development/SKILL.md
git commit -m "docs(subagent-driven-development): Add Checkpoint Pauses section"
```

---

### Task 10: Update Advantages section

**What the user can do after this task:** See accurate comparison with executing-plans

**Files:**
- Modify: `skills/subagent-driven-development/SKILL.md:180-183`

**Step 1: Update the vs. Executing Plans comparison**

Find lines 180-183:
```markdown
**vs. Executing Plans:**
- Same session (no handoff)
- Continuous progress (no waiting)
- Review checkpoints automatic
```

Replace with:
```markdown
**vs. Executing Plans:**
- Same session (no handoff)
- Two-stage review (spec + quality) per task
- Same checkpoint pauses at phase boundaries
```

**Step 2: Verify the change**

- Read `skills/subagent-driven-development/SKILL.md` Advantages section
- Confirm "vs. Executing Plans" no longer claims "no waiting" or "continuous progress"

**Step 3: Commit**

```bash
git add skills/subagent-driven-development/SKILL.md
git commit -m "docs(subagent-driven-development): Update advantages for checkpoint pauses"
```

---

### Task 11: Update Example Workflow to show checkpoint pause

**What the user can do after this task:** See a concrete example of checkpoint pausing

**Files:**
- Modify: `skills/subagent-driven-development/SKILL.md:96-170` (Example Workflow section)

**Step 1: Update the example to show checkpoint**

Find lines 125-128 in the example (after Task 1 completion):
```markdown
[Mark Task 1 complete]

Task 2: Recovery modes
```

Replace with:
```markdown
[Mark Task 1 complete]
[Task 1 was not a [CHECKPOINT], continue to next task]

Task 2: Recovery modes
```

Then find lines 159-163 (after Task 2):
```markdown
[Mark Task 2 complete]

...

[After all tasks]
```

Replace with:
```markdown
[Mark Task 2 complete]
[Task 2 has [CHECKPOINT] in title - pause for feedback]

You: "Phase 1 complete. Tasks 1-2 finished. Verification file updated.
Ready for feedback."

User: "Looks good, continue."

Task 3: ...
```

**Step 2: Verify the change**

- Read `skills/subagent-driven-development/SKILL.md` Example Workflow section
- Confirm checkpoint pause is shown between Task 2 and Task 3

**Step 3: Commit**

```bash
git add skills/subagent-driven-development/SKILL.md
git commit -m "docs(subagent-driven-development): Show checkpoint pause in example"
```

---

### Task 12: [CHECKPOINT] Verify subagent-driven-development skill is complete

**What the user can do after this task:** Confirm subagent-driven-development correctly implements checkpoint pauses

**Files:**
- Review: `skills/subagent-driven-development/SKILL.md`

**Step 1: Full skill review**

Read entire `skills/subagent-driven-development/SKILL.md` and verify:
- [ ] Flowchart includes checkpoint decision node after "Mark task complete"
- [ ] Checkpoint Pauses section exists with detection and reporting instructions
- [ ] Advantages section no longer claims "no human-in-loop" or "continuous progress"
- [ ] Example Workflow shows checkpoint pause between tasks
- [ ] No conflicting instructions about continuous execution

**Step 2: Update test-verification file**

Append to `docs/plans/2026-02-09-checkpoint-markers-test-verification.md`:

```markdown
## Phase 3: Update subagent-driven-development Skill
**Verification performed:** [Today's date]
**What was tested:**
- Read skills/subagent-driven-development/SKILL.md
- Confirmed flowchart includes checkpoint decision node
- Confirmed Checkpoint Pauses section exists with detection logic
- Confirmed Advantages section updated (no "continuous progress" claim)
- Confirmed Example Workflow shows checkpoint pause
- No conflicting continuous execution instructions found
**Result:** PASS
```

**Step 3: Commit verification file**

```bash
git add docs/plans/2026-02-09-checkpoint-markers-test-verification.md
git commit -m "docs: Add phase 3 verification for checkpoint markers"
```
