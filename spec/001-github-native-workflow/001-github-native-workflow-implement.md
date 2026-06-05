# Implementation Plan: GitHub-Native Spec-Driven Workflow

## Execution Order

Tasks execute sequentially — each comment format defined in an earlier task is reused verbatim in later tasks.

---

## Task 1 — Update specify skill

**Goal:** specify posts its output to a GitHub issue comment instead of a local file.

**Changes to `.claude/skills/specify/SKILL.md`:**

Add a new section "GitHub Issue Mode" after the existing "Stage 2 — Build (autonomous)" description:

```
## GitHub Issue Mode

When the user's trigger phrase contains an issue number (e.g. "pick up issue #42", "spec out issue #7"):

1. Extract the issue number N from the trigger phrase.
2. Read the issue body with `mcp__github__issue_read` to use as the initial requirement context.
3. Run the Stage 1 clarify Q&A using the issue body as the starting brief. Ask follow-up questions as needed in the conversation (not on the issue).
4. Run Stage 2 (write the spec) autonomously.
5. Instead of writing `spec/NNN-slug/NNN-slug-specify.md`, post the spec as an issue comment using `mcp__github__add_issue_comment`. The comment body must begin with the exact header:
   ## 📋 Spec
6. Do NOT create any local spec/ directory or file.
7. Proceed immediately to the tasks skill, passing the issue number N as context.
```

The existing Q&A, full spec, and lightweight spec logic is unchanged — only the output destination changes.

---

## Task 2 — Update tasks skill

**Goal:** tasks reads the spec from the issue comment and posts its output as an issue comment.

**Changes to `.claude/skills/tasks/SKILL.md`:**

Replace the opening "Find the spec at `spec/NNN-slug/...`" sentence with:

```
## GitHub Issue Mode

When an issue number N is in context (passed from the specify skill):

1. Read the issue's comments using `mcp__github__issue_read` and locate the comment beginning with `## 📋 Spec`. Use that comment's content as the spec input.
2. Run the existing planning (Phase 2) and task-breakdown (Phase 3) logic unchanged.
3. Instead of writing `spec/NNN-slug/NNN-slug-tasks.md`, post the tasks as an issue comment using `mcp__github__add_issue_comment`. The comment body must begin with the exact header:
   ## 📋 Tasks
4. Do NOT create any local tasks file.
5. Immediately write the implementation plan and post it as an issue comment using `mcp__github__add_issue_comment`. The comment body must begin with the exact header:
   ## 📋 Implementation Plan
6. Do NOT create any local implement file.
```

For non-GitHub mode (no issue number in context), the existing file-based behavior is unchanged.

---

## Task 3 — Update implement skill

**Goal:** implement reads from issue comments, creates a linked PR, auto-subscribes, handles review/CI, and edits comments for drift.

**Changes to `.claude/skills/implement/SKILL.md`:**

Add a "GitHub Issue Mode" section after the "Core Principle":

```
## GitHub Issue Mode

When an issue number N is in context:

### Reading the spec
Read the issue's comments and locate:
- `## 📋 Spec` — requirement context
- `## 📋 Tasks` — task list and acceptance criteria
- `## 📋 Implementation Plan` — execution order

Use these in place of any local spec/ files.

### PR creation
When creating a PR for this implementation:
- Include "Closes #N" in the PR body so GitHub auto-links and auto-closes the issue on merge.
- Immediately after the PR is created, call `mcp__github__subscribe_pr_activity` to watch the PR.

### Handling review comments
When a `<github-webhook-activity>` event arrives with a review comment:
1. Read the comment to understand what change is requested.
2. Push a fix on the PR branch.
3. Reply to the comment thread on the PR using `mcp__github__add_reply_to_pull_request_comment` to confirm what was changed.

### Handling CI failures
When a `<github-webhook-activity>` event signals a CI failure:
1. Fetch the failing job logs with `mcp__github__get_job_logs`.
2. Diagnose the root cause.
3. Push a fix on the PR branch.
4. If CI is re-triggerable, do so; otherwise wait for the next event.

### Spec drift
When an implementation increment reveals that a spec assumption, boundary, or requirement has changed:
1. Edit the `## 📋 Spec` issue comment in-place using `mcp__github__issue_write` (do not post a new comment).
2. Notify the human before proceeding to the next task.
```

The per-task approval gate, increment cycle, and implementation rules are unchanged.

---

## Task 4 — Update CLAUDE.md

**Goal:** CLAUDE.md's "Spec-Driven Development" section accurately describes the GitHub-native workflow.

**Changes to `CLAUDE.md`:**

Replace the "Spec-Driven Development" section with:

```markdown
## 0. Spec-Driven Development

**Before implementing any feature, run the spec skill.**

Invoke the `specify` skill before writing any code for new features.

### GitHub-Native Workflow (primary)

1. **Issue** — Create a GitHub issue describing the feature or task.
2. **Trigger** — Tell Claude: "pick up issue #N". Claude reads the issue body and enters the clarify phase.
3. **Spec** — After Q&A, Claude posts three structured comments on the issue:
   - `## 📋 Spec` — requirements and success criteria
   - `## 📋 Tasks` — breakdown with acceptance criteria
   - `## 📋 Implementation Plan` — execution order
4. **Implement** — Claude executes tasks one at a time, presenting each for approval before proceeding. The PR includes "Closes #N".
5. **Watch** — Claude auto-subscribes to the PR. Review comments and CI failures are handled automatically without re-triggering.

Issue labels track state: `needs-spec` → `specced` → `in-progress` → `in-review`.

No local `spec/` directory is created.

### Interaction Modes

1. **Clarify** — Q&A to establish direction and scope
2. **Build** — spec/tasks/plan written autonomously without pausing
3. **Implement** — one task at a time; present summary, wait for approval; on rejection, cascade changes through all three issue comments and re-present
```

Remove the "Spec File Naming & Structure" subsection (no longer applicable).
```

---

## Task 5 — Documentation audit

**Goal:** No stale references to local spec/ files remain in skill or instruction files.

Run a search for `spec/NNN` and `spec/\d\d\d` across `.claude/skills/` and `CLAUDE.md`. For any hit, either update the reference to describe the GitHub comment approach or remove the stale line.
