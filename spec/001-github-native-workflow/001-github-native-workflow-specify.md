# Spec: GitHub-Native Spec-Driven Workflow

## Objective

Replace local `spec/` file storage with GitHub issues as the primary workflow driver. Today, specs live as markdown files on disk — invisible to collaborators, disconnected from the task tracker, and requiring manual linking to PRs. This feature makes GitHub the single source of truth: issues capture tasks, spec phases are stored as structured issue comments, implementation creates a linked PR, and Claude auto-watches that PR for review comments and CI failures. The developer experience collapses to: create an issue, tell Claude to pick it up, then comment on the PR.

## User Stories

- As a developer, I want to create a GitHub issue and have Claude generate a full spec on it so that I can review and discuss the spec alongside the code without switching contexts.
- As a developer, I want to comment on a PR and have Claude automatically update the implementation so that I don't need to re-trigger anything manually.
- As a developer, I want CI failures on a PR to be auto-diagnosed and fixed so that I don't babysit the build.

## Functional Requirements

- FR-1: When the user says "pick up issue #N", Claude reads the issue body from GitHub and enters the specify clarify phase.
- FR-2: After the clarify phase, Claude posts the completed spec as a structured comment on the issue (prefixed `## 📋 Spec`) instead of writing a local `specify.md`.
- FR-3: After the spec comment, Claude posts the tasks breakdown as a structured comment on the issue (prefixed `## 📋 Tasks`) instead of writing a local `tasks.md`.
- FR-4: After the tasks comment, Claude posts the implementation plan as a structured comment on the issue (prefixed `## 📋 Implementation Plan`) instead of writing a local `implement.md`.
- FR-5: The implement skill reads spec, tasks, and implementation plan from the issue's comments rather than local files.
- FR-6: Each implementation PR includes "Closes #N" in its description, linking it to the source issue.
- FR-7: After creating a PR, Claude automatically calls `subscribe_pr_activity` to watch for review comments, CI events, and approvals.
- FR-8: Incoming review comments are handled automatically: Claude pushes a fix and replies on the PR thread.
- FR-9: CI failures on a watched PR are auto-diagnosed; Claude pushes a fix and re-triggers CI.
- FR-10: Spec drift during implementation updates the relevant issue comment in-place (edit, not a new comment).
- FR-11: Issue labels track workflow state: `needs-spec` → `specced` → `in-progress` → `in-review`.

## Non-Functional Requirements

- NFR-1: Skills remain independently invocable — the GitHub path activates only when an issue number is in context; no breaking change for users who don't use GitHub issues.
- NFR-2: Issue comments use consistent section headers so they are machine-readable by subsequent skill invocations.

## Out of Scope

- Automated issue creation (user creates issues manually).
- Webhook or GitHub Actions triggers (user triggers Claude manually with "pick up issue #N").
- Auto-merge on PR approval.
- Multi-repository support.
- Migrating any existing local spec files to GitHub.

## Assumptions

- The GitHub MCP server (`mcp__github__*`) is available in every session where this workflow runs.
- The repository is `shubhsheth/template-repo`; the issue number is always provided explicitly by the user.
- One issue maps to one PR (no multi-PR features in this iteration).

## Tech Stack

- Skill system: markdown files under `.claude/skills/`
- GitHub integration: `mcp__github__*` MCP tools (issue_read, issue_write, add_issue_comment, create_pull_request, subscribe_pr_activity)
- No new runtime dependencies

## Commands

```
No new build/test commands — this is a skill/workflow change only.
Verify manually by running the workflow end-to-end against a test issue.
```

## Project Structure

```
.claude/skills/specify/SKILL.md      → updated: post spec to GitHub issue
.claude/skills/tasks/SKILL.md        → updated: read spec from issue, post tasks to issue
.claude/skills/implement/SKILL.md    → updated: read from issue, link PR, auto-subscribe
CLAUDE.md                            → updated: describe GitHub-native workflow
spec/                                → no longer created for future features (this spec is the last local one)
```

## Code Style

Skills are plain markdown with embedded instruction blocks. No code is generated — only instruction files are edited. Match existing heading hierarchy and tone in each skill file.

## Testing Strategy

Manual end-to-end verification against a real GitHub issue in the repository:
1. Create a test issue with a brief task description.
2. Trigger "pick up issue #N" and confirm the three spec comments appear on the issue.
3. Approve the first implement task and confirm a PR is created with "Closes #N".
4. Confirm `subscribe_pr_activity` is called.
5. Post a review comment on the PR and confirm Claude pushes a fix.

No automated tests — these are workflow instruction files, not executable code.

## Boundaries

- Always: include "Closes #N" in every PR created from an issue; call `subscribe_pr_activity` after every PR creation; use the exact comment prefix headers so downstream parsing works.
- Ask first: changing the label names or adding new labels beyond the four defined; posting more than one spec/tasks/implement comment per issue.
- Never: delete or overwrite a human's comments on an issue or PR; create a PR without a linked issue number; push directly to main.

## Success Criteria

- A developer can say "pick up issue #N" and within one session receive three structured spec comments on the issue with no local files created.
- Every PR created through this workflow contains "Closes #N" and is automatically watched.
- A review comment on a watched PR results in a pushed fix within the same session, without the developer re-triggering anything.
- A CI failure on a watched PR is diagnosed and a fix is pushed automatically.
- The existing skills continue to work for users not using GitHub issues (no regression).

## Open Questions

None — direction confirmed.
