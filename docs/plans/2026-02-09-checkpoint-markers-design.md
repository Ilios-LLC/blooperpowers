# Checkpoint Markers Design

**User Entrypoint:** Documentation review (skill markdown correctness)

**Verification Method:** Review skill markdown for correct instructions

**User-Facing Capabilities:**
- Write plans where `[CHECKPOINT]` marks phase boundaries
- Execute plans (either skill) with automatic pauses at phase boundaries
- Get consistent pause behavior across both execution skills
- Use legacy plans (no markers) with fallback batch-of-3 behavior

---

## Problem

The subagent-driven-development skill does not pause between phases for user verification, while executing-plans does. This inconsistency means users get different experiences depending on which execution skill they use.

Additionally, executing-plans pauses after arbitrary batches of 3 tasks, which may not align with meaningful phase boundaries in the plan.

## Solution

Introduce a `[CHECKPOINT]` marker convention that:
1. Plan authors add to the last task of each phase
2. Execution skills detect and pause after

This pushes phase boundary awareness into the plan structure rather than requiring execution skills to parse phase headers or maintain metadata.

---

## The `[CHECKPOINT]` Convention

**Marker format:** `[CHECKPOINT]` appears in the task title

**Example:**
```markdown
### Task 4: [CHECKPOINT] Verify user can see empty recipe list
```

**Semantics:**
- Marks the final task of a phase
- Task must include UEP verification
- Task must write to the test-verification file
- Execution skills pause after completing this task

**Detection:** Simple string match - `[CHECKPOINT]` in task name/subject

---

## Changes to writing-plans

**Current behavior:**
- Says "last planned task in each phase MUST be complete testing"
- No explicit marker in task titles

**New behavior:**
- Add `[CHECKPOINT]` to the last task title of each phase
- Add instruction that `[CHECKPOINT]` tasks must write to test-verification file

**Specific changes to SKILL.md:**

1. **Add to Plan Document Header section** - mention that `[CHECKPOINT]` markers signal phase boundaries

2. **Update Task Structure template** - show `[CHECKPOINT]` in the phase-ending task example:
   ```markdown
   ### Task N: [CHECKPOINT] [User-Facing Outcome]
   ```

3. **Add new step to `[CHECKPOINT]` tasks** - "Write to test-verification file" with the append format already defined in executing-plans/subagent-driven-development

4. **Add brief explanation** - `[CHECKPOINT]` tasks trigger execution pauses for user review

---

## Changes to subagent-driven-development

**Current behavior:**
- Executes all tasks sequentially without pausing
- Controller writes to verification file at phase completion (but no pause)
- Flowchart shows: `"More tasks remain?" → "yes" → "Dispatch implementer subagent"`

**New behavior:**
- After completing any `[CHECKPOINT]` task, pause for user feedback
- Report what was completed and verification evidence
- Wait for user before continuing to next task

**Specific changes to SKILL.md:**

1. **Update flowchart** - Add decision node after "Mark task complete":
   ```
   "Was task a [CHECKPOINT]?" → "yes" → "Report and wait for feedback"
   "Was task a [CHECKPOINT]?" → "no" → "More tasks remain?"
   ```

2. **Add new section: "Checkpoint Pauses"** explaining:
   - Detect `[CHECKPOINT]` in task subject
   - Report: completed tasks since last checkpoint, verification file contents
   - Say: "Phase complete. Ready for feedback."
   - Wait for user response before continuing

3. **Update Example Workflow** - Show the pause happening after a `[CHECKPOINT]` task

4. **Update "Advantages" section** - Remove "no human-in-loop between tasks" (no longer true), replace with "human checkpoints at phase boundaries"

---

## Changes to executing-plans

**Current behavior:**
- Batches by fixed count (default 3 tasks)
- Pauses after each batch regardless of content
- Says "Ready for feedback" between batches

**New behavior:**
- Execute tasks until hitting a `[CHECKPOINT]` task
- Complete the `[CHECKPOINT]` task, then pause
- No fixed batch size - batch boundaries defined by `[CHECKPOINT]` markers

**Specific changes to SKILL.md:**

1. **Update Step 2: Execute Batch** - Replace "Default: First 3 tasks" with:
   - "Execute tasks until completing a `[CHECKPOINT]` task"
   - "If no `[CHECKPOINT]` found, execute all remaining tasks"

2. **Update Step 3: Report** - Clarify this happens after `[CHECKPOINT]` tasks

3. **Add fallback behavior** - If plan has no `[CHECKPOINT]` markers (legacy plans), fall back to batch-of-3 behavior

4. **Update "Remember" section** - Add bullet about `[CHECKPOINT]` detection

---

## Files to Modify

- `skills/writing-plans/SKILL.md` - Add `[CHECKPOINT]` convention to task templates
- `skills/subagent-driven-development/SKILL.md` - Add checkpoint detection and pause logic
- `skills/executing-plans/SKILL.md` - Replace batch-of-3 with checkpoint-based pauses

## What Stays the Same

- Test-verification file format and location
- UEP verification requirements
- Two-stage review in subagent-driven-development
- All other skill behavior
