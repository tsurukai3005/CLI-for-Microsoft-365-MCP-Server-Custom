---
allowed-tools: TodoWrite, TodoRead, Read, Write, MultiEdit, Bash(mkdir:*)
description: Create requirements specification for the given task (Stage 1 of Spec-Driven Development)
---

## Context

- Task description: $ARGUMENTS

## Your task

### 1. Create directory

- Create `.tmp` directory if it doesn't exist

### 2. Analyze the user's request

Carefully analyze the provided task description and extract:

- The core problem to be solved
- Implicit requirements not explicitly stated
- Potential edge cases and constraints
- Success criteria

### 3. Create Requirements Document

Create `.tmp/requirements.md` with the following sections:

```markdown
# Requirements Specification - [Task Name]

## 1. Purpose

[Clearly describe the purpose of this task/project]

## 2. Functional Requirements

### 2.1 Core Features

- [ ] [Detailed description of feature 1]
- [ ] [Detailed description of feature 2]
      ...

### 2.2 Optional Features

- [ ] [Features that could be implemented in the future]
      ...

## 3. Non-Functional Requirements

### 3.1 Performance

- [Requirements for response time, processing speed, etc.]

### 3.2 Security

- [Security-related requirements]

### 3.3 Maintainability

- [Requirements for code maintainability]

### 3.4 Compatibility

- [Compatibility requirements with existing systems]

## 4. Constraints

### 4.1 Technical Constraints

- [Constraints on technologies, libraries to be used]

### 4.2 Business Constraints

- [Constraints such as delivery deadlines, budget, etc.]

## 5. Success Criteria

### 5.1 Definition of Done

- [ ] [Clear completion condition 1]
- [ ] [Clear completion condition 2]
      ...

### 5.2 Acceptance Tests

- [Conditions for user satisfaction]

## 6. Potential Risks

- [Implementation risks and countermeasures]

## 7. Future Considerations

- [Items to be detailed in the design phase]
```

### 4. Create TODO entry

Use TodoWrite to add "Complete and review requirements definition" as a task

### 5. Present to user

Show the created requirements document and ask for:

- Confirmation of understanding
- Any missing requirements
- Approval to proceed to design phase

## Important Notes

- Be thorough in identifying implicit requirements
- Consider both current needs and future extensibility
- Use clear, unambiguous language
- Include measurable success criteria

think hard
