# Plan: Skill Suite Revision

## Component Map

```
CLAUDE.md                              (independent — soften mandate)
incremental-implementation/SKILL.md   (independent — add drift checkpoint)
planning-and-task-breakdown/SKILL.md  (absorb PLAN + TASKS from spec skill)
spec-driven-development/SKILL.md      (shrink to SPECIFY only + lightweight path)
```

No circular dependencies. All four files can be edited independently.

## Implementation Order

1. **`incremental-implementation/SKILL.md`** — smallest change (one new step in the increment cycle). Independent baseline.
2. **`CLAUDE.md`** — one-line mandate change. Independent.
3. **`planning-and-task-breakdown/SKILL.md`** — absorbs PLAN + TASKS phase content from spec skill. Needs to be done before spec skill so the cross-reference target exists.
4. **`spec-driven-development/SKILL.md`** — largest change: removes PLAN/TASKS phases, adds lightweight path, adds cross-references to planning skill.

## Change Detail Per File

### `incremental-implementation/SKILL.md`
- Add one step to The Increment Cycle between step 4 (commit) and step 5 (proceed):
  > "Check spec drift: if this increment changed any assumption, boundary, or requirement, update the spec file before proceeding."
- No other changes. Scope discipline stays here as canonical source.

### `CLAUDE.md`
- Replace:
  > "ALWAYS invoke the `spec-driven-development` skill first by calling `/spec-driven-development` before writing any code. Do not skip this step even if the requirements seem clear."
- With:
  > "Invoke the `spec-driven-development` skill before writing any code for new features. The skill defines when a full spec, a lightweight spec, or no spec is needed — defer to its 'When to Use' criteria rather than applying it unconditionally."

### `planning-and-task-breakdown/SKILL.md`
- Rewrite as a proper skill file with frontmatter (matching sibling skills).
- Open with: "This skill owns the PLAN and TASKS phases of the spec-driven workflow. It receives a validated spec from `spec-driven-development` as its input."
- Absorb Phase 2 (Plan) content from spec skill: component identification, dependency ordering, risk notes, parallel vs sequential work, verification checkpoints.
- Absorb Phase 3 (Tasks) content from spec skill: task template, task sizing, documentation update task requirement, QA gate checklist.
- Remove: references to scope discipline (canonical in incremental-implementation).
- Remove: the four-phase overview diagram (owned by spec skill).

### `spec-driven-development/SKILL.md`
- Update the gated workflow diagram to show delegation:
  ```
  SPECIFY ──→ planning-and-task-breakdown ──→ incremental-implementation
     │                    │                              │
     ▼                    ▼                              ▼
   Human               Human                          Human
   reviews             reviews                        reviews
  ```
- Remove Phase 2 (Plan) section entirely.
- Remove Phase 3 (Tasks) section entirely.
- Add after Phase 1: "Hand off to `planning-and-task-breakdown` for PLAN and TASKS phases."
- Add Phase 4 (Implement) reference: "Execute using `incremental-implementation`."
- Add **Lightweight Spec Path** section before the full spec template:
  - Trigger criteria: multi-file change, < 30 min estimated, requirements are clear (no ambiguity)
  - Short-form template: Objective + Success Criteria + Out of Scope only
  - Gate: human confirms "lightweight is appropriate" before proceeding
- Update Verification checklist: remove "Boundaries defined" item (not required for lightweight spec); add "Confirm whether full or lightweight spec was used."
- Keep: Overview, When to Use/Not Use, Phase 1 (Specify), Keeping the Spec Alive, Common Rationalizations, Red Flags.

## Risks

- **Spec skill becomes too thin**: Mitigate by keeping all Phase 1 content intact — only phases 2 and 3 are removed.
- **Planning skill feels disconnected**: Mitigate by opening with explicit "receives spec as input" framing and linking back to spec skill.
- **Lightweight path is abused**: Mitigate by requiring human confirmation that lightweight is appropriate before proceeding.
