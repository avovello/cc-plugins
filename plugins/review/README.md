# Review Plugin 👁️

Multi-perspective code review with automated quality checks and iterative improvement.

## Overview

The Review Plugin provides comprehensive code review from multiple perspectives: code quality, security, performance, and architecture. It includes a review-fix loop to ensure all feedback is addressed.

## Workflow Diagram

```
┌──────────────────┐
│  Gather Changes  │
└────────┬─────────┘
         │
         ▼
┌──────────────────────┐
│   Code Quality       │
│      Review          │
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│   Security Review    │
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│  Performance Review  │
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│ Architecture Review  │
└────────┬─────────────┘
         │
         ▼
┌──────────────────────┐
│  Consolidate         │
│  Feedback            │
└────────┬─────────────┘
         │ approved?
         │ no
         ├──────┐
         │      ▼
         │  ┌─────────────┐
         │  │Apply Fixes  │◄───┐
         │  └──────┬──────┘    │
         │         ▼            │
         │  ┌─────────────┐    │
         │  │  Re-review  │────┘
         │  └─────────────┘
         │ yes
         ▼
┌──────────────────────┐
│   Final Report       │
└──────────────────────┘
```

## Review Perspectives

### 1. Code Quality Review
- **Readability**: Clear variable names, proper formatting
- **Maintainability**: DRY principle, proper abstractions
- **Error Handling**: Proper try-catch, error messages
- **Testing**: Test coverage, edge cases
- **Documentation**: Comments, JSDoc/docstrings

### 2. Security Review
- **Input Validation**: All inputs validated
- **Authentication/Authorization**: Proper access controls
- **Data Protection**: Sensitive data encrypted
- **Injection Prevention**: SQL injection, XSS prevention
- **Secrets Management**: No hardcoded secrets

### 3. Performance Review
- **Efficiency**: Optimal algorithms, no unnecessary loops
- **Resource Usage**: Memory leaks, connection pooling
- **Scalability**: Can it handle growth?
- **Caching**: Appropriate caching strategies
- **Database**: Efficient queries, proper indexing

### 4. Architecture Review
- **Design Patterns**: Appropriate patterns used
- **Separation of Concerns**: Clean architecture
- **Dependencies**: Minimal coupling
- **SOLID Principles**: Following best practices
- **Future Extensibility**: Easy to extend

## Processing Loops

### Review-Fix Loop
- **Review** → (if issues) **Apply Fixes** → **Re-review** → (repeat until approved)
- Maximum 2 iterations by default
- Each iteration focuses on remaining issues

## Configuration

```yaml
# .claude/review-config.yaml
review:
  perspectives:
    - code_quality: true
    - security: true
    - performance: false  # Optional
    - architecture: true

  strictness: high # low | medium | high

  max_review_iterations: 2

  auto_fix:
    enabled: false  # Automatically fix obvious issues
    types:
      - formatting
      - simple_refactoring

  blocking_issues:
    - security_critical
    - breaking_changes
```

## Usage

### Via Slash Command
```bash
/review "src/auth/*.js"
```

### Via Workflow
```bash
claude-code workflow review \
  --files="src/**/*.js" \
  --perspectives="code_quality,security,performance"
```

## Subagents

- **Code Quality Reviewer**: Reviews code quality and maintainability
- **Security Reviewer**: Identifies security vulnerabilities
- **Performance Reviewer**: Analyzes performance implications
- **Architecture Reviewer**: Reviews architectural decisions

## Output

The review produces:

1. **Review Report** (`REVIEW_REPORT.md`)
   - Summary of findings
   - Issues by severity (critical, high, medium, low)
   - Perspective-specific feedback

2. **Action Items** (`REVIEW_ACTION_ITEMS.md`)
   - Prioritized list of issues to fix
   - Specific file and line numbers
   - Recommendations for each issue

## Success Criteria

Review is complete when:
- [x] All perspectives reviewed
- [x] Feedback consolidated
- [x] Critical issues addressed
- [x] Final approval given

## Examples

See `examples/review/` for examples of different review types.
