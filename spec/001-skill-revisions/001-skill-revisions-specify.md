# Spec: Revise Spec/Planning Skill Suite

## Objective

Revise the three `.claude/skills/` files (`spec-driven-development`, `planning-and-task-breakdown`, `incremental-implementation`) and `CLAUDE.md` to:
- Eliminate redundant content across skills
- Give each skill a single, clear ownership domain
- Add a lightweight spec path for medium-complexity tasks
- Address spec drift during incremental implementation
- Align `CLAUDE.md`'s mandate with the spec skill's own "When NOT to Use" guidance

The audience is the AI agent (Claude) reading these skill files at runtime. Changes should make each skill more focused and easier to follow without duplicating guidance from the others.

## User Stories

- As a developer, I want the spec skill to focus on capturing requirements so I don't wade through planning and execution content when I just need to nail down what we're building.
- As a developer, I want a lightweight spec option so that medium-complexity tasks get some structure without the overhead of a full 8-section spec.
- As a developer, I want spec drift to be explicitly addressed so the spec stays useful throughout implementation, not just at the start.
- As a developer, I want `CLAUDE.md` to match the skill's own guidance so the "always run spec" mandate doesn't create friction on clearly scoped work.

## Functional Requirements

- FR-1: `spec-driven-development` owns only the SPECIFY phase. PLAN and TASKS phases are delegated to `planning-and-task-breakdown` by explicit reference.
- FR-2: `spec-driven-development` contains a lightweight spec path (short-form template: objective + success criteria + out of scope) for medium-complexity tasks that don't warrant the full template.
- FR-3: `planning-and-task-breakdown` explicitly states it receives a validated spec as input and owns PLAN + TASKS phases.
- FR-4: Scope discipline rules ("touch only what the task requires") live exclusively in `incremental-implementation`. References to this in other skills are removed.
- FR-5: `incremental-implementation` adds a spec drift checkpoint: after each increment, verify whether the spec needs updating before proceeding to the next.
- FR-6: `CLAUDE.md` replaces the unconditional "ALWAYS invoke spec-driven-development" mandate with language that defers to the skill's own "When NOT to Use" criteria.
- FR-7: Acceptance criteria / verification checklist language is consolidated — it lives in `planning-and-task-breakdown` as the canonical source.

## Non-Functional Requirements

- NFR-1: Each skill file should be readable end-to-end in under 3 minutes (roughly 600 words or less of net-new prose per file after removing duplicated sections).
- NFR-2: No skill file should reference its own content as "the spec" — cross-references between skills should use the skill name explicitly.

## Out of Scope

- Creating new skill files (e.g., a standalone lightweight-spec skill or a verifier skill)
- Changing the four-phase model itself (SPECIFY → PLAN → TASKS → IMPLEMENT)
- Updating any files outside `.claude/skills/` and `CLAUDE.md`
- Rewriting the skills from scratch — surgical edits only

## Assumptions

- The skill files are read by Claude at runtime, not by humans primarily
- The four-phase SPECIFY → PLAN → TASKS → IMPLEMENT pipeline stays intact; only ownership of each phase changes
- "Lightweight spec" means: objective + success criteria + out of scope (3 sections, no user stories / tech stack / commands / project structure / code style / testing strategy / boundaries)
- The lightweight path is triggered when a task is medium-complexity (multi-file, < 30 minutes, unambiguous requirements) — between the "trivial skip" and "full spec" thresholds
- Spec drift checkpoint = a single bullet in the increment cycle: after verifying the increment, check if any spec section needs updating and update it before committing

## Tech Stack

Markdown files only. No code changes.

## Commands

```
Verify: Read each updated skill file and confirm removed sections are gone, added sections are present
```

## Project Structure

```
.claude/skills/spec-driven-development/SKILL.md   → owns SPECIFY phase
.claude/skills/planning-and-task-breakdown/SKILL.md → owns PLAN + TASKS phases
.claude/skills/incremental-implementation/SKILL.md  → owns IMPLEMENT phase + spec drift checkpoint
CLAUDE.md                                           → updated mandate
spec/001-skill-revisions/                           → this spec
```

## Success Criteria

- SC-1: `spec-driven-development/SKILL.md` contains no PLAN or TASKS phase content of its own; it explicitly delegates to `planning-and-task-breakdown` for those phases.
- SC-2: `spec-driven-development/SKILL.md` contains a lightweight spec template with exactly 3 sections (objective, success criteria, out of scope) and clear trigger criteria.
- SC-3: `planning-and-task-breakdown/SKILL.md` opens with a statement that it receives a validated spec as input.
- SC-4: Scope discipline ("touch only what the task requires") appears in `incremental-implementation/SKILL.md` only — not in the other two skills.
- SC-5: `incremental-implementation/SKILL.md`'s increment cycle includes a spec drift checkpoint step.
- SC-6: `CLAUDE.md`'s spec mandate references the skill's "When NOT to Use" criteria instead of using "ALWAYS."
- SC-7: No section in any skill file is a near-duplicate of a section in another skill file.

## Open Questions

None — all clarified with human before spec was written.
