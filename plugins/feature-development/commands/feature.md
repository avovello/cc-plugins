---
description: Systematic feature development with codebase exploration, clarifying questions, and architecture design (SOLID, Clean Architecture, Design Patterns, DDD)
---

# Feature Development Command

Systematic feature development: understand the codebase, resolve ambiguities, design architecture, then implement.

## Usage

```bash
/feature "Add user authentication with OAuth2"
```

## Workflow

### Phase 1: Discovery

Clarify what needs to be built.

1. Gather context: problem, users, constraints, success criteria
2. Summarize requirements back to user, identify gaps
3. Get confirmation before proceeding

**Output**: Clear problem statement and requirements summary

### Phase 2: Codebase Exploration

Understand relevant existing code at both high and low levels.

Launch parallel Explore agents:

```
Task(subagent_type="Explore", prompt="Trace how [similar feature] works end-to-end")
Task(subagent_type="Explore", prompt="Map architecture layers for [relevant domain]")
Task(subagent_type="Explore", prompt="Find all integration points for [system component]")
Task(subagent_type="Explore", prompt="Analyze data models and relationships for [domain]")
```

**Output**: Codebase analysis with file references and patterns

### Phase 3: Clarifying Questions

Resolve ALL ambiguities before designing.

> **CRITICAL**: DO NOT proceed until all questions are answered.

Present unresolved ambiguities:
- Edge cases, error handling, performance, security, backward compatibility, design preferences

```markdown
## Questions Requiring Answers

### Must Answer (Blocking)
1. [Critical question]

### Should Clarify (Recommended)
1. [Important question]
```

**PAUSE**: Wait for user answers before Phase 4.

### Phase 4: Architecture Design

Design robust architecture using proven principles.

```
Task(subagent_type="feature-development:code-architect", prompt="Design architecture for [feature] given exploration results: [summary]")
```

The code-architect applies:

| Skill | Purpose |
|-------|---------|
| **solid-principles** | SRP, OCP, LSP, ISP, DIP |
| **clean-architecture** | Layered structure with Dependency Rule |
| **design-patterns** | Creational, Structural, Behavioral patterns |
| **domain-driven-design** | Bounded Contexts, Aggregates, Domain Events |

**PAUSE**: Wait for user to approve architecture before implementing.

### Phase 5: Implementation

Build the feature following approved architecture.

> Only proceed after explicit user approval.

1. Follow codebase conventions discovered in Phase 2
2. Implement in logical order (data models → services → APIs → UI)
3. Handle edge cases identified in Phase 3

### Phase 6: Summary

Document what was built.

```markdown
## Feature Implementation Summary

### Completed
- [x] Component 1 implemented
- [x] Component 2 implemented

### Key Decisions
1. Used [approach] because [reason]

### Files Changed
- src/services/Feature.js (new)
- src/routes/index.js (modified)

### Next Steps
1. [Follow-up work]
```
