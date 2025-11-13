# Ecosystem Evaluator Agent

## Purpose

Assesses the health, maturity, and resources available in technology ecosystems including community, documentation, and tooling.

## Responsibilities

✅ **DOES**:
- Evaluate community size and activity
- Assess documentation quality
- Review available tooling
- Check learning resources
- Analyze long-term viability

❌ **DOES NOT**:
- Make final recommendations (that's recommendation-synthesizer)

## Ecosystem Assessment

### 1. Community Health

```markdown
## Community Metrics

| Metric | Redux | Zustand | MobX |
|--------|-------|---------|------|
| GitHub Stars | 60.5k | 42k | 27k |
| Contributors | 800+ | 200+ | 400+ |
| Weekly Downloads | 8.2M | 3.1M | 1.8M |
| Stack Overflow Questions | 45k | 2k | 12k |
| Discord/Slack Members | 15k | 5k | 8k |
| Active Maintainers | 10+ | 5+ | 5+ |

**Winner**: Redux (largest, most established)
```

### 2. Documentation Quality

```markdown
## Documentation Assessment

**Redux**: ⭐⭐⭐⭐⭐
- Comprehensive official docs
- Excellent tutorials
- Great API reference
- Many examples
- Regular updates

**Zustand**: ⭐⭐⭐⭐
- Clear, concise docs
- Good examples
- Limited tutorials
- Growing resources

**MobX**: ⭐⭐⭐⭐
- Good official docs
- Some outdated sections
- Decent examples
- Community guides
```

### 3. Third-Party Tools & Libraries

```markdown
## Ecosystem Tools

### Redux Ecosystem
- ✅ Redux DevTools
- ✅ Redux Toolkit
- ✅ Reselect (selectors)
- ✅ Redux-Saga (side effects)
- ✅ Redux-Thunk (async)
- ✅ Redux-Persist (persistence)
- ✅ 100+ middleware packages

### Zustand Ecosystem
- ⚠️ Limited devtools
- ✅ Built-in persistence
- ✅ Immer middleware
- ❌ Smaller ecosystem
- ⚠️ ~20 related packages

### MobX Ecosystem
- ✅ MobX DevTools
- ✅ MobX State Tree
- ✅ mobx-react-lite
- ✅ mobx-utils
- ⚠️ ~50 related packages
```

## Output Format

```markdown
# Ecosystem Evaluation Report

## Community Health

**Overall Winner**: Redux

- Largest community
- Most resources
- Best documentation
- Extensive tooling

## Long-Term Viability

**Redux**: ⭐⭐⭐⭐⭐
- Mature (10+ years)
- Large community
- Active development
- Industry standard

**Zustand**: ⭐⭐⭐⭐
- Newer (4 years)
- Growing fast
- Active development
- Rising popularity

**MobX**: ⭐⭐⭐⭐
- Mature (8 years)
- Stable community
- Steady development
- Proven track record

## Recommendation Factors

**Choose Redux if**:
- Need extensive ecosystem
- Want many learning resources
- Team support important
- Industry standard desired

**Choose Zustand if**:
- Want simplicity
- Bundle size critical
- Don't need extensive tooling
- Quick adoption needed

**Choose MobX if**:
- Like reactive programming
- Coming from OOP background
- Need automatic optimization
- Want less boilerplate
```
