# Research Plugin 📚

Technology research, API exploration, and best practice discovery with comparative analysis.

## Overview

The Research Plugin helps you research technologies, APIs, libraries, and best practices. It provides structured research with comparison, evaluation, and actionable recommendations.

## Workflow Diagram

```
┌──────────────────┐
│  Define Scope    │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│   Research       │
│   (Parallel)     │
│  ┌────────────┐  │
│  │ Option A   │  │
│  ├────────────┤  │
│  │ Option B   │  │
│  ├────────────┤  │
│  │ Option C   │  │
│  └────────────┘  │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Compare Options │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Evaluate Trade- │
│      offs        │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Generate        │
│  Recommendation  │
└────────┬─────────┘
         │
         ▼
┌──────────────────┐
│  Create POC      │
│  (Optional)      │
└──────────────────┘
```

## Research Types

### 1. Technology Evaluation
Research and compare technologies/libraries for a specific use case.

**Example**: "Compare React, Vue, and Svelte for building a dashboard"

**Process**:
- Research each technology
- Identify strengths and weaknesses
- Compare on key criteria
- Recommend best fit

### 2. API Exploration
Understand and document external APIs.

**Example**: "Research GitHub GraphQL API for repository analytics"

**Process**:
- Explore API documentation
- Identify relevant endpoints/queries
- Test API capabilities
- Document usage patterns

### 3. Best Practices Discovery
Research best practices for specific scenarios.

**Example**: "Best practices for handling authentication in microservices"

**Process**:
- Research industry standards
- Find proven patterns
- Identify common pitfalls
- Recommend approach

### 4. Problem-Solution Research
Research solutions to specific technical problems.

**Example**: "How to optimize database queries for time-series data"

**Process**:
- Understand the problem
- Research available solutions
- Compare approaches
- Provide implementation guidance

## Workflow Phases

### Phase 1: Define Research Scope
- Clarify research question
- Identify options to research
- Define evaluation criteria
- Set research boundaries

### Phase 2: Conduct Research
For each option being researched:
- **Gather Information**
  - Official documentation
  - Community resources
  - Real-world usage examples
  - Recent updates/trends

- **Analyze**
  - Features and capabilities
  - Limitations and constraints
  - Learning curve
  - Community and support
  - Performance characteristics
  - Cost (if applicable)

### Phase 3: Compare Options
- Create comparison matrix
- Evaluate against criteria
- Identify trade-offs
- Note unique advantages

### Phase 4: Evaluate and Recommend
- Assess fit for use case
- Consider context and constraints
- Provide recommendation
- Explain reasoning

### Phase 5: Create POC (Optional)
- Build proof-of-concept with recommended option
- Validate assumptions
- Document findings

## Subagents

- **Researcher**: Gathers information on specific topics
- **Evaluator**: Analyzes and evaluates options
- **Comparator**: Creates comparative analyses

## Configuration

```yaml
# .claude/research-config.yaml
research:
  depth: moderate # quick | moderate | deep

  sources:
    - official_docs
    - community_resources
    - code_examples
    - benchmarks

  comparison_criteria:
    - performance
    - ease_of_use
    - community_support
    - maintenance
    - cost

  create_poc: false  # Auto-create proof of concept
```

## Usage

### Via Slash Command
```bash
/research "Compare ORMs for Node.js: Prisma vs TypeORM vs Sequelize"
```

### Via Workflow
```bash
claude-code workflow research \
  --topic="GraphQL vs REST for mobile app" \
  --depth=deep \
  --create-poc=true
```

## Output

The research produces:

1. **Research Report** (`RESEARCH_REPORT.md`)
   - Executive summary
   - Detailed findings for each option
   - Comparison matrix
   - Recommendation with reasoning

2. **Reference Links** (`REFERENCES.md`)
   - Documentation links
   - Useful resources
   - Code examples
   - Community discussions

3. **POC Code** (if enabled)
   - Working proof-of-concept
   - Implementation notes

## Success Criteria

Research is complete when:
- [x] All options researched
- [x] Comparison completed
- [x] Clear recommendation provided
- [x] Trade-offs documented
- [x] Actionable next steps defined

## Examples

See `examples/research/` for example research projects.
