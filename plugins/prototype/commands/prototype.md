---
description: Quick experimentation through rapid prototyping, A/B testing, and data-driven decision making
---

# Prototype Command

**Purpose**: Quick experimentation through rapid prototyping, A/B testing, and data-driven decision making

## Overview

The prototype command enables rapid experimentation to validate ideas before full implementation. Research shows that **prototyping reduces development waste by 40-60%**.

**Key Distinction**: Prototype creates lightweight POCs for validation, while Feature builds production-ready implementations.

## Workflow

### Step 1: Plan Experiment
- Define hypothesis
- Identify metrics
- Design experiment
- Estimate effort

### Step 2: Implement POC
- Build minimal working version
- Focus on validation, not production quality
- Use mocks and simplifications
- Implement quickly

### Step 3: Test Experiment
- Run with real users (if applicable)
- Collect metrics
- Gather feedback
- Document results

### Step 4: Analyze Results
- Compare to hypothesis
- Analyze metrics
- Identify insights
- Make recommendation

### Step 5: Decide
- Go/No-Go decision
- Plan full implementation (if go)
- Document learnings

### Step 6: Cleanup
- Remove prototype code (if no-go)
- Archive experiment data
- Share learnings

## Usage Examples

```bash
/prototype "test new recommendation algorithm"
/prototype "experiment with dark mode UI"
/prototype "A/B test new checkout flow"
/prototype "validate API design for new feature"
```

## Output

```
prototype-output/
├── EXPERIMENT_PLAN.md        # Experiment design
├── IMPLEMENTATION.md         # What was built
├── RESULTS.md                # Experiment results
├── ANALYSIS.md               # Data analysis
├── DECISION.md               # Go/No-Go decision
└── LEARNINGS.md              # Key insights
```

**Priority**: P3 - MEDIUM
