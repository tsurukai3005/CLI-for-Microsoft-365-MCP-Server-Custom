# Development Rules

## Core Principles

- **Minimalism is Key**:
    - Implement only the essential features required to meet the objective. Avoid over-engineering.
    - Do not add excessive debugging features or mode-switching functionalities.
- **Configuration-Driven**:
    - Centralize all parameters and settings in one place whenever feasible to improve maintainability.
    - An exception is when configurations serve entirely distinct roles; in such cases, separating them into different management locations is preferable for clarity.
- **Data Integrity and Validation**:
    - **IMPORTANT**: Do NOT assume configuration values are correct. Validate that required sections and fields exist, and fail explicitly with a clear error message when they are missing.
    - **NEVER**: Use dummy data or placeholders for missing values. The priority is to identify and flag the absence of data as a critical issue.
    - **NEVER**: Implement complex error handling for multiple data formats. Expect a single, consistent format.
- **Code Consolidation (DRY Principle) - CRITICAL**:
    - **YOU MUST**: Consolidate any recurring calculation or logic into a single, reusable function.
    - **NEVER**: Disperse processes with the same role across different parts of the codebase.
    - **FORBIDDEN**: Implementing the same algorithm logic in multiple files, even for testing or verification purposes.
    - **ENFORCEMENT**: Before implementing any algorithm, you MUST first check if it already exists in the core modules. If it exists, you MUST use the existing implementation.
- **Impact Analysis (CRITICAL)**:
    - **You must** carefully verify that your changes do not negatively affect other parts of the system. This is a top priority.
    - When modifying a function, trace all its call sites and ensure that argument and return types are updated correctly across the entire codebase.
    - If changes affect other functionalities, ensure that the existing behavior remains correct and meets expectations.
- **Handling Complex Code**:
    - **IMPORTANT**: For any complex type definitions, you must add comments and clear usage examples.
    - **YOU MUST**: Be able to explain the working principles of any code you generate.
- **Backward Compatibility (CRITICAL)**:
    - **IMPORTANT**: Maintaining backward compatibility is not required. We will standardize on the new functionalities.
    - An old feature can only be kept if it serves a distinct and necessary role. The role is what matters.

## Programming Rules

- Avoid hard-coding values unless absolutely necessary.
- **Function Usage Verification (MANDATORY)**:
    - **BEFORE** using any function, you MUST verify:
        1. The function actually exists in the target module (use Read or Grep tool)
        2. The function signature matches your intended usage (parameters, types, return values)
        3. The function's purpose aligns with your requirements
    - **NEVER** assume a function exists based on naming conventions or typical patterns
    - **ALWAYS** check the exact parameter order, names, and types
    - **FORBIDDEN**: Calling functions without prior verification of their existence and signature

## TypeScript-Specific Rules

- Use `strict: true` in tsconfig — do not weaken type checks.
- Prefer `const` over `let`; never use `var`.
- Use explicit return types on exported functions.
- Use ESM imports (`import`/`export`), not CommonJS (`require`/`module.exports`).
- Handle errors with typed error classes or discriminated unions, not generic `catch(e: any)`.

## Unified Planning

### Principles

- **One plan, one document**: All planning goes into a single `.tmp/plan_{feature_name}.md`
- **Split tasks, not documents**: If a plan exceeds ~150 lines, the task itself is too large. Break it into independent sub-features and plan each separately.
- **Essence over code**: Plans describe WHAT and WHY, not HOW in code. Specific code belongs to the implementation phase.
- **Visual structure**: Use ASCII diagrams to show data flow, component relationships, and state transitions.

### Plan Document Structure

```
# {Feature Name} Plan

## Goal
One sentence describing the objective.

## Context
- What exists today (brief)
- What problem this solves

## Architecture
Use ASCII diagrams for component relationships and data flow:

    ┌──────────┐     Message      ┌──────────┐
    │ CompA    │ ──────────────▶  │ CompB    │
    └──────────┘                  └──────────┘
         │                             │
         ▼                             ▼
    ┌──────────┐                 ┌──────────┐
    │ State X  │                 │ State Y  │
    └──────────┘                 └──────────┘

## Changes
List each file to modify/create with a one-line description of the change.
- `src/index.ts` - Add new tool handler for X
- `src/util.ts` - New: helper function for Y

## Key Decisions
Bullet list of design decisions and trade-offs.

## Verification
How to confirm the implementation works (manual test steps, expected behavior).

## Tasks
Ordered checklist for TodoWrite. Each task should be independently verifiable.
- [ ] Task 1: ...
- [ ] Task 2: ...
```

### Workflow

```
  User Request
       │
       ▼
  ┌─────────────────┐    Too large?    ┌──────────────────┐
  │ Draft plan in    │ ──────YES──────▶ │ Split into        │
  │ .tmp/plan_*.md   │                  │ sub-features      │
  └─────────────────┘                  └──────────────────┘
       │ NO                                    │
       ▼                                       ▼
  ┌─────────────────┐                  Plan each separately
  │ User confirms   │
  │ plan             │
  └─────────────────┘
       │
       ▼
  ┌─────────────────┐
  │ Implementation   │◀── TodoWrite tracks progress
  │ (per task)       │
  └─────────────────┘
       │
       ▼
  ┌─────────────────┐
  │ Verify & Done    │
  └─────────────────┘
```

1. **Plan**: Draft `.tmp/plan_{feature_name}.md` using the structure above
2. **Confirm**: Present the plan to the user for approval before implementation
3. **Implement**: Execute each task, tracked via TodoWrite
4. **Verify**: Confirm implementation meets the plan's verification criteria

### Rules

- **DO NOT** create separate requirements, design, test-design, and task documents
- **DO NOT** include specific code implementations in the plan (interfaces, method signatures are OK if they clarify architecture)
- **DO** use ASCII diagrams for any non-trivial component interaction
- **DO** keep each task small enough to be completed and verified independently
- Simple fixes or obvious bug fixes can skip planning entirely
