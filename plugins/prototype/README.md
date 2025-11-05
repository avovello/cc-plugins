# Prototype Plugin

Quick experimentation through rapid prototyping, A/B testing, and data-driven decision making.

## Overview

The Prototype plugin enables rapid experimentation to validate ideas before full implementation. Research shows that **prototyping reduces development waste by 40-60%**.

**Key Distinction**:
- **Prototype** creates lightweight POCs for validation (this plugin)
- **Feature** builds production-ready implementations

## Use Cases

- **Algorithm Testing**: Test new algorithms before full implementation
- **UI/UX Experiments**: Validate design changes with users
- **A/B Testing**: Compare different approaches with real data
- **API Design Validation**: Test API design before building
- **Technical Spike**: Explore new technologies or approaches
- **Performance Testing**: Test if approach meets performance requirements

## Usage

```bash
/prototype "test new recommendation algorithm"
/prototype "experiment with dark mode UI"
/prototype "A/B test new checkout flow"
/prototype "validate API design for new feature"
```

## Specialized Agents (6)

### 1. **experiment-planner**
Designs experiments with clear hypotheses and metrics

### 2. **quick-implementer**
Rapidly builds prototypes focused on validation

### 3. **results-analyzer**
Analyzes experiment results with statistical rigor

### 4. **decision-synthesizer**
Makes Go/No-Go recommendations based on data

### 5. **cleanup-agent**
Cleans up prototype code and archives experiments

### 6. **ab-test-setup**
Sets up A/B testing infrastructure

## Example Output

```
✅ Experiment planned: "Test new recommendation algorithm"
✅ Hypothesis: New algorithm improves CTR by 15%
✅ Metrics: CTR, conversion rate, revenue per user

✅ Prototype implemented in 2 hours
✅ A/B test running: 50% traffic to new algorithm

Results after 1 week:
- CTR: +18% (hypothesis confirmed ✅)
- Conversion: +12%
- Revenue: +15%

Recommendation: GO - Proceed with full implementation 🚀
```

## Output

```
prototype-output/
├── EXPERIMENT_PLAN.md       # Experiment design
├── IMPLEMENTATION.md        # What was built
├── RESULTS.md              # Experiment results
├── ANALYSIS.md             # Data analysis
├── DECISION.md             # Go/No-Go decision
└── LEARNINGS.md            # Key insights
```

**Priority**: P3 - MEDIUM
**Why**: Reduces waste by validating before building
