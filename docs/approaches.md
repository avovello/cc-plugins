# Implementation Approaches for Claude Code Marketplace

This document outlines three different approaches to implementing the processing loops and plugin architecture. Each has trade-offs in terms of complexity, flexibility, and reusability.

## Approach 1: Monolithic Workflows (Simple & Direct)

### Overview
Single workflow file that contains all logic, state management, and orchestration.

### Structure
```
plugins/feature/
├── workflow.yaml           # Single file with everything
└── README.md
```

### Example: Feature Plugin Workflow
```yaml
name: feature-development
description: Complete feature development lifecycle

config:
  max_test_retries: 3
  max_review_iterations: 2

steps:
  - name: planning
    agent: general-purpose
    prompt: |
      Plan the feature implementation. Consider:
      - Architecture changes needed
      - Files to modify/create
      - Testing strategy
      - Potential risks
    outputs:
      - plan

  - name: planning_review
    agent: general-purpose
    prompt: |
      Review the plan from previous step: {{steps.planning.outputs.plan}}
      Check for:
      - Completeness
      - Architectural concerns
      - Missing edge cases
    outputs:
      - review_feedback
      - approved

  - name: apply_planning_feedback
    condition: "!steps.planning_review.outputs.approved"
    agent: general-purpose
    prompt: |
      Revise the plan based on feedback: {{steps.planning_review.outputs.review_feedback}}
    goto: planning_review

  - name: implementation
    agent: general-purpose
    prompt: |
      Implement the approved plan: {{steps.planning.outputs.plan}}

  - name: testing
    agent: general-purpose
    prompt: |
      Run all tests for the implementation
    outputs:
      - test_results
      - all_passed

  - name: bug_analysis
    condition: "!steps.testing.outputs.all_passed"
    agent: general-purpose
    prompt: |
      Analyze test failures: {{steps.testing.outputs.test_results}}
      Identify root causes and plan fixes
    outputs:
      - analysis

  - name: bug_fix
    condition: "!steps.testing.outputs.all_passed"
    agent: general-purpose
    prompt: |
      Fix bugs based on analysis: {{steps.bug_analysis.outputs.analysis}}
    goto: testing
    max_iterations: "{{config.max_test_retries}}"

  - name: code_review
    agent: general-purpose
    prompt: |
      Perform comprehensive code review of all changes
    outputs:
      - review_comments
      - changes_requested

  - name: apply_review_feedback
    condition: "steps.code_review.outputs.changes_requested"
    agent: general-purpose
    prompt: |
      Address review comments: {{steps.code_review.outputs.review_comments}}
    goto: code_review
    max_iterations: "{{config.max_review_iterations}}"

  - name: documentation
    agent: general-purpose
    prompt: |
      Create/update documentation for the feature

  - name: deploy_prep
    agent: general-purpose
    prompt: |
      Prepare deployment checklist and instructions
```

### Pros
- ✅ **Simple**: Everything in one place
- ✅ **Easy to understand**: Linear flow with clear conditionals
- ✅ **State management**: Easy to pass data between steps
- ✅ **Quick setup**: No complex configuration

### Cons
- ❌ **Not reusable**: Logic tied to specific workflow
- ❌ **Hard to test**: Components not isolated
- ❌ **Duplication**: Same logic repeated across plugins

### Best For
- Quick prototypes
- Simple workflows
- Single-use cases
- Learning and experimentation

---

## Approach 2: Modular Composition (Flexible & Reusable)

### Overview
Separate subagents, skills, and workflows that are composed via configuration files.

### Structure
```
plugins/feature/
├── config.yaml             # Plugin configuration
├── workflow.yaml          # High-level workflow
└── README.md

subagents/
├── planner.yaml           # Planning subagent
├── implementer.yaml       # Implementation subagent
├── tester.yaml           # Testing subagent
├── bug-analyzer.yaml     # Bug analysis subagent
├── reviewer.yaml         # Code review subagent
└── documenter.yaml       # Documentation subagent

skills/
├── run-tests.yaml        # Test execution skill
├── analyze-failures.yaml # Failure analysis skill
├── review-code.yaml      # Code review skill
└── generate-docs.yaml    # Documentation generation

hooks/
├── pre-commit.yaml       # Pre-commit validation
├── post-test.yaml        # Post-test analysis
└── on-review-fail.yaml   # Review failure handling

workflows/
└── testing-loop.yaml     # Reusable testing loop
```

### Example: Feature Plugin Configuration
```yaml
# plugins/feature/config.yaml
name: feature-development
description: Complete feature development lifecycle

subagents:
  planner:
    type: planner
    config:
      max_iterations: 2
  implementer:
    type: implementer
  tester:
    type: tester
  bug_analyzer:
    type: bug-analyzer
  reviewer:
    type: reviewer
  documenter:
    type: documenter

skills:
  - run-tests
  - analyze-failures
  - review-code
  - generate-docs

hooks:
  pre-commit:
    - validate-syntax
    - check-formatting
  post-test:
    - analyze-coverage
    - report-failures
  on-review-fail:
    - create-fix-plan

workflow: feature-workflow
```

### Example: Subagent Definition
```yaml
# subagents/bug-analyzer.yaml
name: bug-analyzer
description: Analyzes test failures and identifies root causes

base_agent: general-purpose

tools:
  - Read
  - Grep
  - Bash

prompt_template: |
  You are a bug analysis specialist. Analyze test failures and identify root causes.

  Test Results:
  {{test_results}}

  Your analysis should include:
  1. Summary of failures
  2. Root cause identification
  3. Affected components
  4. Recommended fixes
  5. Risk assessment

outputs:
  - failure_summary
  - root_causes
  - affected_files
  - fix_recommendations
  - risk_level

validation:
  required_outputs:
    - root_causes
    - fix_recommendations
```

### Example: Skill Definition
```yaml
# skills/run-tests.yaml
name: run-tests
description: Execute test suite and collect results

parameters:
  test_path:
    type: string
    default: "./tests"
  test_command:
    type: string
    default: "npm test"
  coverage:
    type: boolean
    default: true

steps:
  - name: detect_test_framework
    bash: |
      if [ -f "package.json" ]; then
        cat package.json | grep -E "jest|mocha|vitest"
      fi
    outputs:
      - framework

  - name: run_tests
    bash: "{{parameters.test_command}}"
    outputs:
      - exit_code
      - stdout
      - stderr

  - name: parse_results
    script: |
      Parse test output and extract:
      - Total tests
      - Passed
      - Failed
      - Test details
    outputs:
      - test_summary
      - failed_tests

  - name: coverage_report
    condition: "parameters.coverage"
    bash: "npm run coverage"
    outputs:
      - coverage_data

outputs:
  all_passed: "{{steps.parse_results.outputs.failed_tests.length == 0}}"
  test_results: "{{steps.parse_results.outputs}}"
  coverage: "{{steps.coverage_report.outputs.coverage_data}}"
```

### Example: Reusable Workflow
```yaml
# workflows/testing-loop.yaml
name: testing-loop
description: Test → Analyze → Fix → Retest loop

parameters:
  max_iterations: 3

subagents:
  - tester
  - bug-analyzer

skills:
  - run-tests
  - analyze-failures

steps:
  - name: test
    skill: run-tests
    outputs:
      - all_passed
      - test_results

  - name: check_success
    condition: "steps.test.outputs.all_passed"
    action: complete

  - name: analyze
    subagent: bug-analyzer
    inputs:
      test_results: "{{steps.test.outputs.test_results}}"
    outputs:
      - fix_recommendations

  - name: fix
    subagent: implementer
    inputs:
      fix_plan: "{{steps.analyze.outputs.fix_recommendations}}"

  - name: retest
    goto: test
    max_iterations: "{{parameters.max_iterations}}"
    on_max_reached: escalate
```

### Example: Hook Definition
```yaml
# hooks/post-test.yaml
name: post-test
description: Runs after test execution

triggers:
  - event: test_completed

conditions:
  - "test_results.failed > 0"

actions:
  - name: analyze_coverage
    skill: analyze-coverage

  - name: notify
    type: notification
    message: |
      Tests failed: {{test_results.failed}} failures
      Coverage: {{coverage.percentage}}%

  - name: create_report
    type: file
    path: "./test-reports/latest.md"
    content: |
      # Test Report
      Failed: {{test_results.failed}}
      {{test_results.details}}
```

### Pros
- ✅ **Highly reusable**: Components shared across plugins
- ✅ **Modular**: Easy to test and maintain
- ✅ **Flexible**: Mix and match components
- ✅ **Scalable**: Add new plugins easily
- ✅ **Professional**: Clean separation of concerns

### Cons
- ❌ **Complex setup**: More files and configuration
- ❌ **Learning curve**: Need to understand composition model
- ❌ **Debugging**: Harder to trace execution flow

### Best For
- Production systems
- Large organizations
- Multiple projects
- Long-term maintenance
- Team collaboration

---

## Approach 3: Hybrid (Pragmatic Balance)

### Overview
Core workflows with pluggable subagents and hooks. Workflows are self-contained but can delegate to specialized subagents and invoke hooks at key points.

### Structure
```
plugins/feature/
├── workflow.yaml          # Main workflow with delegation
├── subagents/            # Plugin-specific subagents
│   ├── planner.yaml
│   └── reviewer.yaml
├── hooks/                # Plugin-specific hooks
│   └── on-test-fail.yaml
└── README.md

subagents/                 # Shared subagents
├── tester.yaml
├── bug-analyzer.yaml
└── documenter.yaml

skills/                    # Shared skills
└── run-tests.yaml

workflows/                 # Shared workflow components
└── testing-loop.yaml
```

### Example: Feature Plugin Workflow (Hybrid)
```yaml
# plugins/feature/workflow.yaml
name: feature-development
description: Complete feature development with pluggable components

config:
  max_test_retries: 3
  max_review_iterations: 2

# Use plugin-specific subagents
subagents:
  planner: ./subagents/planner.yaml
  reviewer: ./subagents/reviewer.yaml

# Import shared subagents
imports:
  subagents:
    - tester: ../../subagents/tester.yaml
    - bug_analyzer: ../../subagents/bug-analyzer.yaml
    - documenter: ../../subagents/documenter.yaml
  workflows:
    - testing_loop: ../../workflows/testing-loop.yaml
  skills:
    - run_tests: ../../skills/run-tests.yaml

# Define workflow with delegation
steps:
  # Planning phase
  - name: planning
    subagent: planner
    outputs:
      - plan

  - name: planning_review
    subagent: reviewer
    inputs:
      content: "{{steps.planning.outputs.plan}}"
      review_type: "planning"
    outputs:
      - approved
      - feedback
    loop:
      condition: "!outputs.approved"
      max_iterations: 2
      action: revise_plan

  # Implementation phase
  - name: implementation
    agent: general-purpose
    prompt: |
      Implement the approved plan: {{steps.planning.outputs.plan}}
    hooks:
      pre: validate-implementation-plan
      post: format-code

  # Testing phase with reusable loop
  - name: testing_loop
    workflow: testing_loop
    config:
      max_iterations: "{{config.max_test_retries}}"
    hooks:
      on_failure: ./hooks/on-test-fail.yaml
    outputs:
      - all_passed
      - final_results

  # Review phase
  - name: code_review
    subagent: reviewer
    inputs:
      content: all_changes
      review_type: "code"
    outputs:
      - approved
      - changes_requested
      - comments
    loop:
      condition: "outputs.changes_requested"
      max_iterations: "{{config.max_review_iterations}}"
      action: apply_feedback

  # Documentation phase
  - name: documentation
    subagent: documenter
    inputs:
      feature_description: "{{steps.planning.outputs.plan}}"
      implementation_details: "{{steps.implementation.outputs}}"

  # Deployment preparation
  - name: deploy_prep
    agent: general-purpose
    prompt: |
      Create deployment checklist:
      - Pre-deployment verification
      - Deployment steps
      - Rollback plan
      - Post-deployment validation
```

### Example: Plugin-Specific Subagent
```yaml
# plugins/feature/subagents/planner.yaml
name: feature-planner
description: Plans feature implementation with architecture considerations

base_agent: general-purpose

tools:
  - Read
  - Grep
  - Task

prompt_template: |
  You are a senior software architect planning a new feature.

  Feature Request:
  {{feature_request}}

  Existing Codebase Context:
  {{codebase_context}}

  Create a comprehensive plan including:
  1. Architecture Analysis
     - Current architecture
     - Required changes
     - Integration points

  2. Implementation Plan
     - Files to create/modify
     - Key components
     - Dependencies

  3. Testing Strategy
     - Unit tests
     - Integration tests
     - Edge cases

  4. Risk Assessment
     - Technical risks
     - Breaking changes
     - Migration needs

  5. Timeline Estimate
     - Development time
     - Testing time
     - Review cycles

outputs:
  - architecture_analysis
  - implementation_plan
  - testing_strategy
  - risks
  - timeline

validation:
  required_outputs:
    - implementation_plan
    - testing_strategy
  quality_checks:
    - check: "outputs.risks.length > 0"
      message: "Must identify at least one risk"
    - check: "outputs.implementation_plan.files.length > 0"
      message: "Must specify files to change"
```

### Example: Plugin-Specific Hook
```yaml
# plugins/feature/hooks/on-test-fail.yaml
name: on-test-failure
description: Enhanced test failure handling for feature development

triggers:
  - event: test_failed

actions:
  - name: capture_state
    type: snapshot
    includes:
      - test_results
      - recent_changes
      - environment

  - name: detailed_analysis
    subagent: bug-analyzer
    inputs:
      test_results: "{{event.test_results}}"
      context: "{{snapshot}}"
    outputs:
      - analysis

  - name: check_regression
    script: |
      Compare current failures with historical test results
      to identify if this is a new regression
    outputs:
      - is_regression
      - previous_passing_commit

  - name: create_issue
    condition: "outputs.is_regression"
    type: github-issue
    title: "Regression: {{test_results.failed_tests[0].name}}"
    body: |
      ## Regression Detected

      **Failed Tests**: {{test_results.failed}}

      **Analysis**:
      {{analysis.summary}}

      **Last Passing Commit**: {{previous_passing_commit}}

  - name: suggest_fix
    type: notification
    message: |
      Test failures detected. Analysis:
      {{analysis.root_causes}}

      Recommended actions:
      {{analysis.fix_recommendations}}
```

### Pros
- ✅ **Balanced complexity**: Not too simple, not too complex
- ✅ **Reusable core**: Shared components where it makes sense
- ✅ **Customizable**: Plugin-specific overrides
- ✅ **Maintainable**: Clear structure, easy to debug
- ✅ **Pragmatic**: Solves real-world needs
- ✅ **Extensible**: Easy to add new plugins

### Cons
- ⚠️ **Requires discipline**: Need to decide what to share vs. embed
- ⚠️ **Medium complexity**: More than monolithic, less than fully modular

### Best For
- **Production systems** (Recommended)
- Teams of various sizes
- Projects that need customization
- Balance of speed and quality
- Real-world development workflows

---

## Comparison Matrix

| Aspect | Monolithic | Modular | Hybrid |
|--------|-----------|---------|--------|
| **Setup Time** | Fast ⚡ | Slow 🐌 | Medium ⏱️ |
| **Learning Curve** | Easy 📚 | Steep 🏔️ | Moderate ⛰️ |
| **Reusability** | Low ❌ | High ✅ | Medium ⚖️ |
| **Maintenance** | Hard 😰 | Easy 😊 | Moderate 😐 |
| **Flexibility** | Low ❌ | High ✅ | High ✅ |
| **Testing** | Hard ❌ | Easy ✅ | Medium ⚖️ |
| **Performance** | Fast ⚡ | Medium ⏱️ | Fast ⚡ |
| **Debugging** | Easy 🔍 | Hard 🔬 | Medium 🔎 |
| **Scalability** | Poor ❌ | Excellent ✅ | Good ✓ |
| **Team Collab** | Poor ❌ | Excellent ✅ | Good ✓ |

---

## Recommendations by Use Case

### For Personal Projects
→ **Approach 1 (Monolithic)** or **Approach 3 (Hybrid)**
- Start simple, evolve as needed
- Hybrid when you find yourself copying code

### For Team Projects
→ **Approach 3 (Hybrid)**
- Balance of speed and maintainability
- Easy onboarding for new team members

### For Enterprise/Production
→ **Approach 2 (Modular)** or **Approach 3 (Hybrid)**
- Modular for maximum flexibility
- Hybrid for pragmatic balance

### For Marketplace Distribution
→ **Approach 2 (Modular)** with **Approach 3 (Hybrid)** examples
- Users can mix and match components
- Provide hybrid examples for common use cases

### For Learning/Experimentation
→ **Approach 1 (Monolithic)**
- Quickest to understand
- See everything in one place

---

## Migration Path

You can start with one approach and migrate to another:

### Monolithic → Hybrid
1. Extract repeated logic into shared subagents
2. Move test/review logic to reusable workflows
3. Add hooks for common events

### Hybrid → Modular
1. Extract plugin-specific subagents to shared library
2. Generalize subagent configurations
3. Create skill library from common operations
4. Build workflow composition layer

### Modular → Hybrid (Simplification)
1. Inline rarely-used subagents into workflows
2. Remove over-engineered abstractions
3. Combine tightly-coupled components

---

## Implementation Priority

For the Claude Code Marketplace, we recommend:

1. **Phase 1**: Implement **Approach 1 (Monolithic)** for all plugins
   - Quick validation of workflows
   - Test with real users
   - Gather feedback

2. **Phase 2**: Refactor to **Approach 3 (Hybrid)**
   - Extract common patterns
   - Create shared subagents
   - Add hook system

3. **Phase 3**: Evolve to **Approach 2 (Modular)** for advanced users
   - Full composition system
   - Marketplace for individual components
   - Community contributions

This allows for rapid iteration while building toward a robust, scalable system.
