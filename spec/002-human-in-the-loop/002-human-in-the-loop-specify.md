# Spec: Human-in-the-Loop Workflow Revision

## Objective

Revise the three skill files and CLAUDE.md to change when and how the human is involved. Currently the human reviews between each spec file creation. The new model has three distinct interaction modes: conversational Q&A during SPECIFY to steer direction and define scope; autonomous spec file creation (all three files built without gates once direction is clear); and per-task approval gates during implementation, with full spec cascade on rejection.

## Success Criteria

- SC-1: During SPECIFY, the agent asks clarifying questions conversationally — one or two at a time — before writing any spec content. The human can steer direction, define scope, and answer questions iteratively before a single spec file is created.
- SC-2: Once the direction is clear and confirmed, the agent builds all three spec files (specify, tasks, implement) without asking for human review between them.
- SC-3: During implementation, the agent presents a one-task-at-a-time summary before executing each task and waits for explicit human approval.
- SC-4: On rejection of any task, the agent revises the approach and cascades changes through all three spec files before re-presenting the task summary.
- SC-5: The workflow diagram and gate descriptions in all affected files accurately reflect the new model.

## Out of Scope

- Changing the three-phase pipeline structure (SPECIFY → PLAN → TASKS → IMPLEMENT)
- Changing the spec file format or template
- Changing the lightweight vs. full spec decision criteria
- Changing the task template or quality gate in planning-and-task-breakdown
