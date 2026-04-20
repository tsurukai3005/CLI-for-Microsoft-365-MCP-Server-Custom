# Core Project Rules

## `.tmp/` File Conventions

All temporary design documents must follow this structure:

```
.tmp/
└── YYYY-WNN/              ← ISO week subfolder (e.g. 2026-W13)
    └── MM-DD_HHmm_name.md ← date-time-prefixed filename
```

Rules:
- **Subfolder**: Use the ISO week of the creation date: `YYYY-WNN` (e.g. `2026-W13`). Create the folder if it does not exist.
- **Filename prefix**: Always prefix the filename with the month-day and time: `MM-DD_HHmm_` (e.g. `03-24_1442_plan_mcp_tool.md`).
- **Descriptive name required**: The name after the date-time prefix MUST describe the content clearly enough to understand the topic without opening the file. Generic names are **forbidden**.
  - OK: `03-24_1442_plan_add_sharepoint_list_tool.md`
  - OK: `03-24_1442_workflow_mcp_error_handling.md`
  - NG: `03-24_1442_plan.md` — too vague
  - NG: `03-24_1442_memo.md` — too vague
- **Existing naming patterns** (`plan_*`, `workflow_*`, `code_review_*`, etc.) are kept after the date-time prefix.
- Example: `.tmp/2026-W13/03-27_0930_plan_teams_tool_implementation.md`

## Global Rule Overrides

The following instructions from `~/.claude/rules/` conflict with this project's policy and are **overridden here**:

- **Planning documents**: IGNORE the instruction in `development-workflow.md` to generate separate "PRD, architecture, system_design, tech_doc, task_list" files. Use a single `.tmp/plan_{feature_name}.md` as defined in the Unified Planning section. Over-documentation leads to design drift.
- **`.tmp/` documentation warning**: Ignore any hook warnings about `.md` files in `.tmp/`. This folder is the designated location for all temporary design notes in this project.
