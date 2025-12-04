---
description: Systematic 7-phase feature development with architecture design (SOLID, Clean Architecture, Design Patterns, DDD) and QA-DEV-REVIEW loops
---

# Feature Development Command

**Purpose**: Systematic feature development with deep codebase understanding, clarifying questions, multiple architecture options, and automated QA-DEV-REVIEW loops.

## Usage

```bash
/feature "Add user authentication with OAuth2"

# With E2E browser testing for UI features
/feature "Add shopping cart UI" --e2e
```

## The 7-Phase Workflow

This command guides you through a comprehensive feature development process that prioritizes understanding before building.

---

## Phase 1: Discovery

**Goal**: Clarify what needs to be built

Before any exploration or implementation, understand the requirements:

1. **Gather Context**:
   - What problem are we solving?
   - Who are the users?
   - What are the constraints?
   - What are the success criteria?

2. **Confirm Understanding**:
   - Summarize requirements back to user
   - Identify any obvious gaps or ambiguities
   - Get confirmation before proceeding

**Output**: Clear problem statement and requirements summary

---

## Phase 2: Codebase Exploration

**Goal**: Understand relevant existing code at both high and low levels

Launch **multiple parallel code-explorer agents** to analyze:

1. **Similar Features**: Find existing features that solve related problems
2. **Architecture Patterns**: Identify current patterns and conventions
3. **Integration Points**: Map where the new feature connects to existing code
4. **Data Models**: Understand current data structures

**Parallel Agent Calls**:
```
code-explorer: "Trace how existing [similar feature] works end-to-end"
code-explorer: "Map the architecture layers for [relevant domain]"
code-explorer: "Find all integration points for [system component]"
code-explorer: "Analyze data models and relationships for [domain]"
```

**Output**: Comprehensive codebase analysis with file references and patterns

---

## Phase 3: Clarifying Questions

**Goal**: Resolve ALL ambiguities before proceeding

> **CRITICAL**: This is one of the most important phases. DO NOT proceed until all questions are answered.

After exploration, present ALL unresolved ambiguities:

1. **Edge Cases**: What happens when X fails? What if Y is empty?
2. **Error Handling**: How should errors be handled? User-facing messages?
3. **Performance**: Expected load? Caching requirements?
4. **Security**: Authentication? Authorization? Data sensitivity?
5. **Backward Compatibility**: Breaking changes? Migration needed?
6. **Design Preferences**: UI/UX choices? API design decisions?

**Format**:
```markdown
## Questions Requiring Answers

### Must Answer (Blocking)
1. [Critical question 1]
2. [Critical question 2]

### Should Clarify (Recommended)
1. [Important question 1]
2. [Important question 2]

### Nice to Know (Optional)
1. [Additional question 1]
```

**PAUSE**: Wait for user answers before proceeding to Phase 4.

---

## Phase 4: Architecture Design

**Goal**: Design robust architecture using proven principles and patterns

Launch **code-architect agent** with architecture skills to design the optimal solution.

### Architecture Skills Applied

The code-architect uses the following skills to ensure quality design:

| Skill | Purpose |
|-------|---------|
| **solid-principles** | Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion |
| **clean-architecture** | Layered structure with Dependency Rule (Entities → Use Cases → Adapters → Frameworks) |
| **design-patterns** | Creational, Structural, Behavioral patterns appropriate for the problem |
| **domain-driven-design** | Ubiquitous Language, Bounded Contexts, Strategic/Tactical patterns |

### Design Process

1. **Analyze Requirements** against architecture principles
2. **Identify Applicable Patterns** from codebase exploration
3. **Design Component Structure** following Clean Architecture layers
4. **Apply SOLID Principles** to ensure maintainability
5. **Define Bounded Contexts** if domain complexity warrants

### Architecture Output

**For the Recommended Approach**:
- Component responsibilities (Single Responsibility)
- Abstraction boundaries (Dependency Inversion)
- Extension points (Open/Closed)
- Interface contracts (Interface Segregation)
- Data flow through layers (Clean Architecture)
- Applicable design patterns with rationale

**Trade-offs Documented**:
- Complexity vs simplicity
- Flexibility vs performance
- Consistency with existing codebase

**PAUSE**: Wait for user to approve architecture before implementing.

---

## Phase 5: Implementation

**Goal**: Build the feature following approved architecture

> **IMPORTANT**: Only proceed after explicit user approval of architecture.

**Implementation Process**:
1. Follow codebase conventions discovered in Phase 2
2. Implement in logical order (data models → services → APIs → UI)
3. Add proper error handling and validation
4. Include logging and monitoring hooks
5. Write inline documentation

**Best Practices**:
- Keep functions small and focused
- Follow existing patterns exactly
- Handle edge cases identified in Phase 3
- Don't over-engineer

---

## Phase 6: Quality Review (QA-DEV-REVIEW Loop)

**Goal**: Ensure code quality through automated testing and review loops

### Part A: Testing Loop (max 3 iterations)

```
1. Write Tests
   ↓
2. Run Tests
   ↓
3. Tests Fail? → Analyze Failures → Fix Code → Re-run (loop)
   ↓
4. All Pass → Continue to Review
```

**Test Types**:
- Unit tests for business logic
- Integration tests for component interactions
- API tests for endpoints

**Coverage Target**: 80%+

### Part B: Review Loop (max 2 iterations)

Launch **parallel code-reviewer agents** to assess:

```
code-reviewer: "Check for simplicity and unnecessary complexity"
code-reviewer: "Verify correctness and edge case handling"
code-reviewer: "Ensure project convention adherence"
```

**Review Categories**:
1. **Project Guidelines**: CLAUDE.md compliance, naming conventions
2. **Bug Detection**: Race conditions, null handling, logic errors
3. **Code Quality**: Duplication, error handling, test coverage

**Confidence-Based Filtering**:
- Only report issues with confidence score 80+
- Critical issues grouped separately
- Actionable fixes provided

**Loop Process**:
```
1. Run Quality Checks (linters, formatters, security)
   ↓
2. Parallel Code Review
   ↓
3. Issues Found? → Fix Issues → Re-review (loop)
   ↓
4. All Clean → Continue to Summary
```

### Part C: E2E Testing (Optional)

If `--e2e` flag is provided for UI features:

```
Run QA plugin commands:
- /e2e - Execute Playwright browser tests
- browser-tester agent for interactive testing
```

> **See also**: QA plugin for standalone testing workflows

---

## Phase 7: Summary

**Goal**: Document what was built and next steps

**Summary Contents**:
1. **Accomplishments**: What was implemented
2. **Key Decisions**: Architecture choices and rationale
3. **Files Modified**: List of all changed files
4. **Testing Results**: Coverage and quality metrics
5. **Next Steps**: Recommendations for follow-up work

**Format**:
```markdown
## Feature Implementation Summary

### Completed
- [x] Component 1 implemented
- [x] Component 2 implemented
- [x] Tests written (87% coverage)
- [x] Code review passed

### Key Decisions
1. Used Approach B because [reason]
2. Chose [pattern] for [reason]

### Files Changed
- src/services/FeatureService.js (new)
- src/controllers/FeatureController.js (new)
- src/models/Feature.js (new)
- src/routes/index.js (modified)

### Quality Metrics
- Test Coverage: 87%
- Linter: Pass
- Security Scan: Pass

### Next Steps
1. [Suggested follow-up 1]
2. [Suggested follow-up 2]
```

---

## Specialized Agents

### code-explorer
Deep codebase analysis agent that traces execution paths, maps architecture layers, and documents dependencies.

**Use For**: Understanding existing features, finding patterns, identifying integration points

### code-architect
Architecture design agent that applies SOLID principles, Clean Architecture, Design Patterns, and DDD to generate robust implementation blueprints.

**Use For**: Designing architecture with proven principles, defining component responsibilities, planning data flows

**Skills**: solid-principles, clean-architecture, design-patterns, domain-driven-design

### code-reviewer
Quality review agent with confidence-based issue filtering (80+ threshold).

**Use For**: Bug detection, code quality assessment, convention compliance

---

## Processing Loops

### Testing Loop
- **Purpose**: Ensure all tests pass
- **Max Iterations**: 3
- **Process**: Test → Analyze Failures → Fix → Re-test

### Review Loop
- **Purpose**: Ensure code quality standards
- **Max Iterations**: 2
- **Process**: Review → Fix Issues → Re-review

---

## Key Principles

1. **Understand Before Building**: Read code thoroughly before writing
2. **Ask Questions Early**: Resolve ambiguities before implementation
3. **Present Options**: Show trade-offs, let user decide
4. **Parallel Exploration**: Launch multiple agents for efficiency
5. **Confidence Filtering**: Only report high-confidence issues
6. **Loop Until Done**: Automated fixing until quality gates pass

---

## E2E Testing Integration

For features with UI components, use the `--e2e` flag:

```bash
/feature "Add shopping cart" --e2e
```

This integrates with the **QA plugin** for browser testing:
- Playwright-based E2E tests
- Interactive browser testing via `browser-tester` agent

---

## Version

1.2.0
