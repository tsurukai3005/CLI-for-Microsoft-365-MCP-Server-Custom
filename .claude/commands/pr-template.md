---
allowed-tools: TodoWrite, TodoRead, Read, Write, MultiEdit, Bash(mkdir:*), Bash(gh pr view:*), Bash(gh pr diff:*)
description: Generate PR template with comprehensive description by analyzing pull request changes
---

## PR Template Generator Rules

Generate a PR template based on the pull request specified by the PR number provided in the command arguments.

Usage: `/pr-template <PR_NUMBER>`
Example: `/pr-template 123`

The PR number will be available as `$ARGUMENTS`.

**Template Generation Process:**

1. **Get PR information:** Use the github cli (`gh pr view $ARGUMENTS`) to get the PR details including title, description, and metadata.
2. **Analyze changes:** Use `gh pr diff $ARGUMENTS` to examine all code changes, additions, deletions, and modifications.
3. **Investigate context:** Read relevant source files to understand the context of modified files and their relationships.
4. **Generate template:** Create a comprehensive PR description following the established template format.
5. **Save to .tmp:** Determine the current ISO week folder (`YYYY-WNN`) and current time (`MM-DD_HHmm`). Save the generated template to `.tmp/YYYY-WNN/MM-DD_HHmm_pr-template-<PR_NUMBER>.md`. Create the week subfolder with `mkdir -p` if it does not exist.

**Template Content Requirements:**

Generate content for each section based on PR analysis:

- **概要 (Overview):** Analyze the PR purpose, background context, and main objectives. Write 1-3 concise lines explaining the "why", "what", and "how" of the changes.

- **修正内容・追加内容 (Changes/Additions):** Provide detailed breakdown of all modifications:
  - Categorize changes (bug fixes, new features, refactoring, etc.)
  - **Explain the implemented mechanisms and changes in plain language**
  - Use markdown formatting for readability

- **アーキテクチャ・処理フロー (Architecture/Flow - Recommended):** Illustrate processing flow with diagrams:
  - Use compact ASCII art / box-drawing diagrams (plain text)
  - Visualizing complex processing flows promotes understanding

- **動作確認 (Testing/Verification):** Suggest appropriate testing approaches:
  - Identify critical test scenarios
  - Recommend functionality verification steps
  - Consider edge cases and error conditions

- **レビュー観点 (Review Focus - Optional):** If complex changes are detected:
  - Highlight areas requiring special attention
  - Point out potential risks or concerns

- **補足・参考 (Additional Notes):** Include relevant supplementary information

**Output Requirements:**

- **Write in Japanese:** All template content must be in Japanese
- **Remove template instructions:** Delete all instructional text marked with ">"
- **Use markdown formatting:** Ensure proper markdown structure for readability

**Error Handling:**

- **If PR number is not specified:** Respond with "PRナンバーを指定してください。"
- **If PR is not found:** Respond with "指定されたPRが見つかりません。PRナンバーを確認してください。"
