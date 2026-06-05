# Tasks: GitHub-Native Spec-Driven Workflow

## Implementation Plan

**Components and dependencies (bottom-up):**

1. **specify skill** — gains a GitHub output path (post spec comment to issue). No upstream dependencies.
2. **tasks skill** — gains a GitHub input path (read spec from issue) and GitHub output path (post tasks comment). Depends on specify's comment format being defined first.
3. **implement skill** — gains a GitHub input path (read all three comments), PR linking ("Closes #N"), auto-subscribe, and spec-drift edit. Depends on tasks' comment format.
4. **CLAUDE.md** — updated to describe the new workflow. Depends on all three skills being finalized.

All four tasks are sequential (each depends on the previous format decisions). No parallelism needed — the files are small.

**Risks:**
- Comment format must be stable before tasks/implement are written, otherwise implement's parsing instructions won't match what specify/tasks produce. Mitigate: define the exact comment header strings in the specify update and reuse them verbatim in tasks and implement.
- The implement skill has the most moving parts (read, PR creation, subscribe, drift edits) — keep each instruction atomic and testable.

---

## Task List

- [ ] Task: Update specify skill to post spec output as a GitHub issue comment
  - Acceptance: SKILL.md instructs Claude to (a) capture the issue number from the user's trigger phrase, (b) run the existing Q&A clarify phase unchanged, (c) post the full spec content as a comment beginning with `## 📋 Spec` on the issue, and (d) skip writing any local `specify.md` file. The existing clarify and spec-writing logic is otherwise unmodified.
  - Verify: Manually trigger "pick up issue #N" on a test issue and confirm a `## 📋 Spec` comment appears with no local file created.
  - Files: `.claude/skills/specify/SKILL.md`
  - Size: S

- [ ] Task: Update tasks skill to read spec from issue and post tasks comment
  - Acceptance: SKILL.md instructs Claude to (a) read the `## 📋 Spec` comment from the issue instead of `spec/NNN-slug/NNN-slug-specify.md`, (b) run the existing planning and task-breakdown logic unchanged, (c) post tasks content as a comment beginning with `## 📋 Tasks` on the issue, and (d) skip writing any local `tasks.md` file.
  - Verify: Continuing from the specify task test, confirm a `## 📋 Tasks` comment appears on the issue.
  - Files: `.claude/skills/tasks/SKILL.md`
  - Size: S

- [ ] Task: Update implement skill with GitHub input, PR linking, auto-subscribe, and drift edits
  - Acceptance: SKILL.md instructs Claude to (a) read spec/tasks/plan from the issue's `## 📋 Spec`, `## 📋 Tasks`, and `## 📋 Implementation Plan` comments, (b) include "Closes #N" in every PR description, (c) call `subscribe_pr_activity` immediately after PR creation, (d) handle incoming review comments by pushing a fix and replying on the PR thread, (e) handle CI failures by diagnosing and pushing a fix, (f) edit (not re-post) the relevant issue comment when spec drift is detected. No local implement.md file is written or read.
  - Verify: Confirm a test PR contains "Closes #N"; confirm subscribe_pr_activity is called; post a test review comment and confirm a fix is pushed.
  - Files: `.claude/skills/implement/SKILL.md`
  - Size: M

- [ ] Task: Update CLAUDE.md to describe the GitHub-native workflow
  - Acceptance: CLAUDE.md's "Spec-Driven Development" section describes the new trigger ("pick up issue #N"), the three comment phases, and the PR watch loop. References to `spec/NNN-slug/` file paths are removed or replaced with GitHub issue instructions. Existing boundaries and workflow stages are preserved.
  - Verify: Read CLAUDE.md and confirm it accurately describes the end-to-end workflow with no stale file path references.
  - Files: `CLAUDE.md`
  - Size: S

- [ ] Task: Update documentation
  - Acceptance: Any new commands, workflow steps, or boundaries introduced by this feature are reflected in CLAUDE.md; no stale references to local spec/ files remain in any skill or instruction file.
  - Verify: Search `.claude/skills/` and `CLAUDE.md` for references to `spec/NNN-slug` and confirm none remain.
  - Files: `CLAUDE.md`, `.claude/skills/specify/SKILL.md`, `.claude/skills/tasks/SKILL.md`, `.claude/skills/implement/SKILL.md`
  - Size: XS
