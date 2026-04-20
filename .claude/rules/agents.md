# Agent Orchestration Rules

## Team Development - Agent Communication Log

When working with agent teams, maintain a separate log to record agent collaboration:

**File**: `.tmp/agent_log_{feature_name}.md`

```
# Agent Log: {Feature Name}

## {Timestamp or Sequence} - {Agent Name}
**Task**: What they were working on
**Decision**: Key decisions made and reasoning (1-2 lines)
**Outcome**: Result or finding (1-2 lines)

## {Timestamp or Sequence} - {Agent Name} → {Agent Name}
**Topic**: What was discussed
**Conclusion**: What was agreed upon (1-2 lines)
```

Rules for the agent log:
- Keep each entry to **3 lines max** (Task/Decision/Outcome or Topic/Conclusion)
- Record only **decisions, discoveries, and blockers** - not routine status updates
- Each agent appends to the log when it makes a non-trivial decision or resolves a blocker
- The team lead reviews the log before final verification

## Team Implementation - Parallel Agent Execution

When a task is large enough to benefit from parallel execution, use background Task agents to split the work.

### When to Use

- Task has **2+ independent work streams** that touch different files/directories
- Each stream requires **5+ file changes**
- Streams have **no file-level conflicts** (different directories or files)

### Execution Method

**Use `run_in_background: true` Task agents** (NOT `TeamCreate`).
`TeamCreate` is mailbox-based async and hard to synchronize. Background Tasks are blocking-waitable and more reliable.

```
┌─────────────────────────────────────────────────┐
│ Lead: Preparation (shared prerequisites)        │
│  - Renames, shared type definitions, etc.       │
└──────────────────┬──────────────────────────────┘
                   │
        ┌──────────┴──────────┐
        ▼                     ▼
┌───────────────┐    ┌───────────────┐
│ Task A        │    │ Task B        │
│ run_in_bg     │    │ run_in_bg     │
│ bypassPerms   │    │ bypassPerms   │
└───────┬───────┘    └───────┬───────┘
        │                     │
        ▼                     ▼
  TaskOutput(block)     TaskOutput(block)
        │                     │
        └──────────┬──────────┘
                   ▼
        ┌─────────────────┐
        │ Lead: Verify    │
        │ & Integrate     │
        └─────────────────┘
```

### Prompt Requirements for Each Agent

Each agent starts with **zero context**. The prompt MUST include:
1. **Goal**: What to implement and why
2. **File list**: Exact paths of files to create/modify
3. **Existing code excerpts**: Key interfaces, type signatures, patterns to follow
4. **Module & import conventions**: ESM import patterns, barrel exports
5. **Boundary**: Which files NOT to touch (other agent's territory)
6. **Post-write verification**: "After using Write or Edit, verify with Read"

### Rules

- **NEVER** mark agent work as completed before verifying files exist with Glob/Read
- **NEVER** use `TeamCreate` for implementation tasks — use `run_in_background` Tasks instead
- **ALWAYS** complete shared prerequisites (renames, type definitions) BEFORE launching agents
- **ALWAYS** split work by directory/file to avoid conflicts
- **ALWAYS** set timeout to 300000 (5 min) or higher for implementation agents
- **ALWAYS** include full code context in the prompt — agents cannot read the lead's conversation history
