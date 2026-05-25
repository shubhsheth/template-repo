# Tasks: Skill Suite Revision

## Task List

- [ ] Task: Add spec drift checkpoint to incremental-implementation
  - Acceptance: The Increment Cycle has a new step 5 between "Commit" and "Proceed to next slice" that instructs checking whether the spec needs updating
  - Verify: Read the file and confirm the step is present and correctly numbered
  - Files: `.claude/skills/incremental-implementation/SKILL.md`

- [ ] Task: Soften CLAUDE.md spec mandate
  - Acceptance: Section 0 no longer says "ALWAYS" or "Do not skip this step even if the requirements seem clear"; instead defers to the skill's own When to Use criteria
  - Verify: Read the file and confirm the updated wording
  - Files: `CLAUDE.md`

- [ ] Task: Rewrite planning-and-task-breakdown to own PLAN + TASKS phases
  - Acceptance: File opens with explicit "receives validated spec as input" framing; contains full PLAN phase content (component identification, dependency ordering, risks, parallel vs sequential, checkpoints); contains full TASKS phase content (task template, task sizing, doc-update task requirement, QA gate); has frontmatter matching sibling skills; contains no scope discipline rules
  - Verify: Read the file and confirm all sections are present and scope discipline is absent
  - Files: `.claude/skills/planning-and-task-breakdown/SKILL.md`

- [ ] Task: Revise spec-driven-development to own SPECIFY only + add lightweight path
  - Acceptance: Phases 2 and 3 removed; workflow diagram updated to show delegation to planning-and-task-breakdown; lightweight spec section added with trigger criteria, 3-section template, and human-confirmation gate; verification checklist updated; cross-references to sibling skills present
  - Verify: Read the file and confirm phases 2/3 are gone, lightweight section exists, diagram is updated
  - Files: `.claude/skills/spec-driven-development/SKILL.md`

- [ ] Task: Commit and push all changes
  - Acceptance: All four modified files and the three spec files are committed on branch `claude/spec-skills-review-qix78` and pushed to remote
  - Verify: `git status` shows clean working tree; `git log` shows new commit
  - Files: all changed files
