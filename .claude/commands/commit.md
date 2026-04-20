---
allowed-tools: TodoWrite, Bash, Grep, Read
description: Analyzes GitHub diffs and automatically creates fine-grained commits with rule-based commit messages
---

## Commit Rules

The automatic commit process follows these rules for generating commit messages:

- **Prefix**: Use Angular.js commit guideline prefixes
  - **feat**: A new feature
  - **fix**: A bug fix
  - **docs**: Documentation only changes
  - **style**: Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)
  - **refactor**: A code change that neither fixes a bug nor adds a feature
  - **perf**: A code change that improves performance
  - **test**: Adding missing tests or correcting existing tests
  - **chore**: Changes to the build process or auxiliary tools and libraries
- **Language**: Write commit messages in Japanese
- **Content**: Include reasons, background, and purpose

## Process Overview

Usage: `/commit`

Execute automatic commit processing with the following steps:

1. **Diff Analysis**: Analyze unstaged changes using `git status` and `git diff`
2. **File Classification**: Group changed files by type (new features, bug fixes, refactoring, etc.)
3. **Individual Commits**: Generate appropriate commit messages for each file group and commit
4. **Verification**: Check status after each commit

## Implementation Instructions

When implementing this command, meet the following detailed requirements:

### Required Features

1. **Diff Understanding**:
   - Analyze changed file contents in detail using `git diff`
   - Understand each file's changes (additions, deletions, modifications)
   - Comprehend code context and functionality to infer commit intent

2. **Fine-grained Commits**:
   - Group related changes into single commits when possible
   - Separate changes with different purposes into different commits
   - Basically commit one script at a time

3. **Appropriate Prefix Selection**:
   - Automatically select appropriate prefix based on filename, change content, and context
   - When multiple change types exist, determine prefix based on primary change

4. **Commit Message Generation**:
   - Generate Japanese messages that understand the background and purpose of changes
   - Infer and include reasons for changes
   - Focus on business value and purpose rather than technical details

5. **Staged Execution**:
   - Use TodoWrite tool for task management
   - Confirm change content before each commit
   - Provide detailed explanation and solutions when errors occur

### Processing Flow

1. **Initial Analysis**:
   - Get list of changed files with `git status`
   - Get detailed diff with `git diff`
   - Create task list with TodoWrite

2. **File Grouping**:
   - Group related files by functionality
   - Analyze change intent for each group
   - Determine commit order

3. **Individual Commit Execution**:
   - Stage with `git add` for each file group
   - Generate appropriate commit messages
   - Execute commit with `git commit`
   - Record task completion with TodoWrite

4. **Final Confirmation**:
   - Confirm no remaining changes with `git status`
   - Check commit history

### Error Handling

- Provide detailed error information when commits fail
- Implement retry for pre-commit hook failures
- Report problems like merge conflicts appropriately to user

### Implementation Notes

- Read files when necessary to deeply understand change content
- Make commit messages concise but meaningful
- Display processing steps that users can understand even though automated
