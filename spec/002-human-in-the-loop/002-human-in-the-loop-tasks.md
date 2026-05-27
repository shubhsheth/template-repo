# Tasks: Human-in-the-Loop Workflow Revision

## Component Map

```
spec-driven-development/SKILL.md   → SPECIFY phase: conversational Q&A before spec writing;
                                     no gate between SPECIFY and downstream file creation
planning-and-task-breakdown/SKILL.md → remove human review gate before handoff to
                                       incremental-implementation; files built automatically
incremental-implementation/SKILL.md → add per-task approval gate; define full cascade
                                       procedure on rejection
CLAUDE.md                           → update workflow description to reflect new model
```

## Implementation Order

1. `spec-driven-development/SKILL.md` — defines the new SPECIFY interaction model; must be settled before downstream skills reference it
2. `planning-and-task-breakdown/SKILL.md` — remove the human gate, clarify automatic handoff
3. `incremental-implementation/SKILL.md` — largest change; add approval gate + cascade procedure
4. `CLAUDE.md` — update workflow summary to match

## Task List

- [ ] Task: Revise SPECIFY phase to conversational Q&A model
  - Acceptance: Phase 1 instructs the agent to ask clarifying questions one or two at a time before writing any spec content; includes guidance on when the direction is "clear enough" to proceed to spec writing; removes any instruction to present a complete spec for review before proceeding to tasks/implement files; states that all three spec files are built automatically once direction is confirmed
  - Verify: Read the file; confirm Q&A-first framing is present and no gate exists between spec writing and downstream file creation
  - Files: `.claude/skills/spec-driven-development/SKILL.md`
  - Size: M

- [ ] Task: Remove human review gate from planning-and-task-breakdown
  - Acceptance: The skill no longer instructs the agent to pause for human review before handing off to incremental-implementation; instead it states the tasks and implement files are produced automatically as part of the same continuous spec-building pass
  - Verify: Read the file; confirm no "wait for human approval" instruction exists between Phase 2/3 and implementation handoff
  - Files: `.claude/skills/planning-and-task-breakdown/SKILL.md`
  - Size: S

- [ ] Task: Add per-task approval gate and cascade procedure to incremental-implementation
  - Acceptance: Before executing each task the agent presents a one-paragraph summary (task name, what will change, files touched) and waits for explicit approval; on rejection the agent updates all three spec files to reflect the revised approach before re-presenting; on approval it proceeds with the increment cycle as normal
  - Verify: Read the file; confirm approval gate, summary format, and full-cascade-on-rejection procedure are all present
  - Files: `.claude/skills/incremental-implementation/SKILL.md`
  - Size: M

- [ ] Task: Update CLAUDE.md workflow description
  - Acceptance: Section 0 accurately describes the new three-mode model: conversational Q&A → autonomous spec creation → per-task approval during implementation
  - Verify: Read the file; confirm the description matches the new model
  - Files: `CLAUDE.md`
  - Size: XS

- [ ] Task: Update spec files to reflect completion
  - Acceptance: This implement file has all tasks marked complete
  - Verify: Review the implement file
  - Files: `spec/002-human-in-the-loop/002-human-in-the-loop-implement.md`
  - Size: XS
