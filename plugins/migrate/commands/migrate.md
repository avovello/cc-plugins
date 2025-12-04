---
description: Framework upgrades, database migrations, and API version migrations with zero-downtime strategies
---

# Migrate Command

**Purpose**: Systematic framework upgrades, database migrations, and API version migrations with zero-downtime strategies and comprehensive rollback planning

## Overview

The migrate command handles complex migrations that are essential for keeping tech stacks modern: React 17→18, Vue 2→3, PostgreSQL 12→15, Python 2→3, API v1→v2, etc.

**Key Distinction**:
- **Migrate** changes underlying frameworks/databases
- **Refactor** improves code structure without changing behavior
- **Feature** adds new functionality

## Workflow

### Step 1: Validate Migration Target

1. **Parse migration request**
   - Source version (React 17, PostgreSQL 12, etc.)
   - Target version (React 18, PostgreSQL 15, etc.)
   - Scope (full codebase, specific modules)

2. **Validate feasibility**
   - Check current version
   - Verify target version exists
   - Assess compatibility

3. **Identify migration type**
   - Framework upgrade (React, Vue, Angular, Django)
   - Database migration (PostgreSQL, MySQL, MongoDB)
   - Language upgrade (Python 2→3, PHP 7→8, Node 14→20)
   - API versioning (v1→v2)
   - Package manager migration (npm→pnpm, bower→npm)

### Step 2: Analyze Breaking Changes

Run **compatibility-analyzer** agent to:
- Fetch official migration guides
- Identify breaking changes
- Scan codebase for affected patterns
- Detect deprecated APIs in use
- Identify incompatible dependencies
- Assess migration complexity
- Estimate effort and risk

**Output**:
```
migration-plan/
├── BREAKING_CHANGES.md       # All breaking changes from docs
├── IMPACT_ANALYSIS.md        # What's affected in codebase
├── DEPENDENCY_ANALYSIS.md    # Dependency compatibility
└── RISK_ASSESSMENT.md        # Migration risks
```

### Step 3: Plan Migration Strategy

Run **migration-planner** agent to:
- Choose migration strategy:
  - **Big Bang**: Full migration at once (lower complexity)
  - **Incremental**: Gradual migration (lower risk)
  - **Parallel Run**: Run old and new side-by-side
  - **Dual-Write**: Write to both, read from one
- Design migration phases
- Plan feature flag strategy
- Design rollback approach
- Create testing strategy
- Define success criteria

**Output**: `MIGRATION_STRATEGY.md` with detailed approach

**Strategy Decision Matrix**:
```
Big Bang Migration:
✅ Simple codebase
✅ Good test coverage
✅ Can afford downtime
❌ Large codebase
❌ Critical 24/7 system

Incremental Migration:
✅ Large codebase
✅ No downtime allowed
✅ Multiple teams
❌ Simple upgrade
❌ Tight deadline

Parallel Run:
✅ Database migrations
✅ Critical systems
✅ Complex business logic
❌ Resource constraints
```

### Step 4: Review Plan (Optional)

Present migration plan to user:
- Migration strategy (Big Bang vs Incremental)
- Breaking changes to address
- Estimated timeline
- Risk mitigation approaches
- Rollback plan

Wait for approval before proceeding.

### Step 5: Prepare Environment

**Pre-Migration Steps**:
1. **Backup**: Create full backup
2. **Baseline**: Capture current metrics
3. **Branch**: Create migration branch
4. **Tests**: Ensure all tests pass
5. **Dependencies**: Update package manager
6. **Documentation**: Backup current docs

### Step 6: Execute Migration

Based on strategy, launch appropriate agents:

#### For Big Bang Migration:

Run **code-transformer** agent:
1. Update dependencies
2. Transform code patterns
3. Update configurations
4. Fix breaking changes
5. Update imports/requires
6. Migrate database schema (if applicable)

#### For Incremental Migration:

Run **dual-write-implementer** agent:
1. **Phase 1: Setup**
   - Add new version dependencies
   - Add feature flags
   - Setup dual environment

2. **Phase 2: Dual Write**
   - Write to both old and new
   - Read from old (stable)
   - Monitor consistency

3. **Phase 3: Dual Read**
   - Write to both
   - Read from new (gradual)
   - Compare results

4. **Phase 4: New Only**
   - Switch fully to new
   - Keep old as fallback
   - Monitor closely

5. **Phase 5: Cleanup**
   - Remove old code
   - Remove feature flags
   - Update documentation

### Step 7: Transform Code

Run **code-transformer** agent for each breaking change:

**Example Transformations**:

**React 17→18**:
```javascript
// Before: ReactDOM.render
import ReactDOM from 'react-dom';
ReactDOM.render(<App />, document.getElementById('root'));

// After: createRoot
import { createRoot } from 'react-dom/client';
const root = createRoot(document.getElementById('root'));
root.render(<App />);
```

**Python 2→3**:
```python
# Before: print statement
print "Hello, World!"

# After: print function
print("Hello, World!")
```

**PostgreSQL 12→15**:
```sql
-- Before: Old syntax
GRANT ALL ON SCHEMA public TO user;

-- After: New syntax
GRANT ALL ON SCHEMA public TO user;
GRANT ALL ON ALL TABLES IN SCHEMA public TO user;
```

### Step 8: Test Migration

Run **migration-tester** agent to:

**Test Levels**:
1. **Unit Tests**: Verify all pass
2. **Integration Tests**: Check module interactions
3. **End-to-End Tests**: Validate user flows
4. **Performance Tests**: Compare before/after
5. **Data Integrity**: Verify data consistency
6. **Backward Compatibility**: Ensure old clients work

**Test Strategy**:
```
For each test suite:
  Run tests → Pass? → Continue
              ↓ Fail
        Analyze → Fix → Re-test → Pass? → Continue
                                     ↓ Fail (after 3 attempts)
                                   Document & Flag
```

### Step 9: Create Rollback Plan

Run **rollback-planner** agent to:
- Document rollback procedure
- Create rollback scripts
- Test rollback process
- Define rollback triggers
- Identify rollback window

**Rollback Triggers**:
- Test failure rate > 10%
- Performance degradation > 20%
- Error rate increase > 5%
- Critical bug discovered
- Data corruption detected

**Output**: `ROLLBACK_PLAN.md` with step-by-step rollback instructions

### Step 10: Execute Cutover

**For Production Migration**:

1. **Pre-Cutover Checklist**:
   - ✅ All tests passing
   - ✅ Rollback plan ready
   - ✅ Backup completed
   - ✅ Team briefed
   - ✅ Monitoring configured
   - ✅ Communication plan ready

2. **Cutover Window**:
   - Choose low-traffic period
   - Enable maintenance mode (if needed)
   - Execute migration
   - Run smoke tests
   - Monitor metrics

3. **Post-Cutover Monitoring**:
   - Error rates
   - Response times
   - User reports
   - System stability

4. **Decision Point** (after 24-48 hours):
   - All clear → Proceed to cleanup
   - Issues found → Rollback

### Step 11: Cleanup (After Successful Migration)

Run **cleanup** agent to:
- Remove old version code
- Remove feature flags
- Remove dual-write logic
- Update dependencies
- Clean up configurations
- Archive old documentation

### Step 12: Document Migration

Run **documentation-updater** agent to:
- Update technical documentation
- Create migration runbook
- Document breaking changes handled
- Update API documentation
- Create team training materials

**Output**:
```
migration-output/
├── MIGRATION_SUMMARY.md      # What was migrated
├── CHANGES_MADE.md           # All code changes
├── BREAKING_CHANGES_FIXED.md # How each was handled
├── ROLLBACK_PLAN.md          # Rollback procedure
├── TESTING_RESULTS.md        # Test outcomes
├── LESSONS_LEARNED.md        # Post-mortem insights
└── TEAM_GUIDE.md             # Developer reference
```

## Migration Strategies in Detail

### Strategy 1: Big Bang Migration

**Best For**: Small to medium codebases, good test coverage, acceptable downtime

**Process**:
```
1. Create migration branch
2. Update all dependencies at once
3. Fix all breaking changes
4. Run full test suite
5. Deploy to staging
6. Test thoroughly
7. Deploy to production (with downtime window)
8. Monitor closely
```

**Pros**:
- ✅ Simpler to execute
- ✅ Faster completion
- ✅ No dual-code maintenance

**Cons**:
- ❌ Higher risk
- ❌ Requires downtime
- ❌ All-or-nothing

### Strategy 2: Incremental Migration

**Best For**: Large codebases, zero-downtime requirement, multiple teams

**Process**:
```
1. Migrate module by module
2. Use feature flags
3. Run old and new in parallel
4. Gradually shift traffic
5. Monitor each phase
6. Rollback capability at each phase
```

**Pros**:
- ✅ Lower risk
- ✅ No downtime
- ✅ Gradual validation

**Cons**:
- ❌ More complex
- ❌ Longer timeline
- ❌ Maintain both versions

### Strategy 3: Parallel Run (Dual-Write)

**Best For**: Database migrations, critical systems, complex business logic

**Process**:
```
Phase 1: Setup
  ├─ Install new database version
  └─ Configure replication

Phase 2: Dual Write
  ├─ Write to both databases
  ├─ Read from old (primary)
  └─ Compare results

Phase 3: Verify Consistency
  ├─ Run reconciliation checks
  └─ Fix any discrepancies

Phase 4: Switch Read
  ├─ Read from new (primary)
  ├─ Write to both
  └─ Monitor closely

Phase 5: Full Cutover
  ├─ New database only
  ├─ Keep old as backup
  └─ Archive after 30 days
```

**Pros**:
- ✅ Zero downtime
- ✅ Continuous validation
- ✅ Easy rollback

**Cons**:
- ❌ 2x write overhead
- ❌ Data consistency complexity
- ❌ Resource intensive

## Configuration

Optional `.claude/migrate-config.yaml`:

```yaml
migrate:
  strategy:
    default: "incremental"         # big_bang, incremental, parallel_run
    allow_downtime: false          # Allow maintenance windows
    max_downtime_minutes: 30       # Max acceptable downtime

  testing:
    run_full_suite: true           # Run all tests
    require_100_percent_pass: true # Must all pass
    performance_threshold: 1.2     # Max 20% slowdown

  rollback:
    auto_rollback_enabled: true    # Auto rollback on failure
    rollback_window_hours: 48      # Time to keep rollback ready
    error_rate_threshold: 0.05     # 5% error rate triggers rollback

  monitoring:
    monitor_duration_hours: 48     # Monitor after cutover
    alert_on_anomalies: true       # Alert on unusual patterns

  documentation:
    create_runbook: true           # Create migration runbook
    update_team_docs: true         # Update team documentation
```

## Usage Examples

### Example 1: React 17 to 18

```bash
/migrate "Upgrade React 17 to 18"

# Output:
# ✅ Detected React 17.0.2 → Target: React 18.2.0
# ✅ Analyzed breaking changes (4 major changes found)
# ✅ Scanned codebase: 156 files affected
#
# Breaking Changes:
# 1. ReactDOM.render → createRoot (45 files)
# 2. Automatic batching behavior (12 files need review)
# 3. Stricter hydration (3 files)
# 4. useEffect timing changes (8 files)
#
# Strategy: Big Bang (good test coverage, can afford 2hr window)
#
# ✅ Updated package.json
# ✅ Transformed 45 ReactDOM.render calls
# ✅ Updated test utilities
# ✅ Fixed hydration warnings
# ✅ All 342 tests pass
#
# Migration complete! React 18.2.0 ✅
```

### Example 2: PostgreSQL 12 to 15

```bash
/migrate "Migrate PostgreSQL 12 to 15"

# Output:
# ✅ Detected PostgreSQL 12.8 → Target: 15.3
# ✅ Analyzed breaking changes (6 changes found)
# ✅ Database size: 45GB, 127 tables
#
# Strategy: Parallel Run (zero-downtime required)
#
# Phase 1: Setup (Day 1)
#   ✅ Provisioned PostgreSQL 15 instance
#   ✅ Configured logical replication
#   ✅ Tested connectivity
#
# Phase 2: Dual Write (Days 2-3)
#   ✅ Enabled dual-write mode
#   ✅ Monitoring replication lag: <100ms
#   ✅ Running consistency checks
#
# Phase 3: Verification (Day 4)
#   ✅ Compared 1M random records: 100% match
#   ✅ Query performance: PG15 10% faster
#   ✅ All indexes migrated
#
# Phase 4: Read Switch (Day 5)
#   ✅ Directed read traffic to PG15
#   ✅ Monitoring query performance
#   ✅ No errors detected
#
# Phase 5: Full Cutover (Day 6)
#   ✅ Switched to PG15 exclusively
#   ✅ PG12 kept as hot backup
#   ✅ 24hr monitoring: all green
#
# Migration complete! PostgreSQL 15.3 ✅
# Rollback available for 48 hours
```

### Example 3: Python 2 to 3

```bash
/migrate "Upgrade Python 2.7 to Python 3.11"

# Output:
# ✅ Detected Python 2.7 → Target: Python 3.11
# ✅ Analyzed breaking changes (15 major changes)
# ✅ Scanned: 234 Python files
#
# Breaking Changes:
# 1. print statement → function (456 occurrences)
# 2. dict.iteritems() → dict.items() (89 occurrences)
# 3. unicode/str handling (123 occurrences)
# 4. integer division (67 occurrences)
# ... and 11 more
#
# Strategy: Incremental (large codebase)
#
# ✅ Phase 1: Automated transformations
#   → Run 2to3 tool
#   → Fixed 1,234 automatic conversions
#   → Tests: 89% pass rate
#
# ✅ Phase 2: Manual fixes (remaining 11%)
#   → Fixed unicode handling (45 files)
#   → Updated dependencies (23 packages)
#   → Tests: 98% pass rate
#
# ✅ Phase 3: Edge cases (remaining 2%)
#   → Fixed custom C extensions (3 files)
#   → Updated async patterns (5 files)
#   → Tests: 100% pass rate
#
# Migration complete! Python 3.11 ✅
# All 1,567 tests passing
```

### Example 4: API v1 to v2

```bash
/migrate "Migrate API from v1 to v2"

# Output:
# ✅ Analyzed API changes
# ✅ Found 23 endpoints affected
#
# Strategy: Incremental with Dual Support
#
# Phase 1: Add v2 Endpoints (Week 1)
#   ✅ Created v2 routes alongside v1
#   ✅ Implemented new response format
#   ✅ Both versions operational
#
# Phase 2: Migrate Clients (Weeks 2-3)
#   ✅ Updated mobile app → v2
#   ✅ Updated web app → v2
#   ✅ Updated partner integrations → v2
#   ✅ 85% of traffic on v2
#
# Phase 3: Deprecation (Week 4)
#   ✅ Added deprecation warnings to v1
#   ✅ Notified remaining clients
#   ✅ 99% of traffic on v2
#
# Phase 4: Sunset v1 (Week 6)
#   ✅ Removed v1 endpoints
#   ✅ Updated documentation
#   ✅ 100% on v2
#
# Migration complete! API v2 ✅
```

## Quality Gates

Before completing migration:

- ✅ All breaking changes addressed
- ✅ Test pass rate: 100%
- ✅ Performance acceptable (< 20% degradation)
- ✅ No critical bugs
- ✅ Rollback plan tested
- ✅ Documentation updated

If any gate fails:
- Report issue
- Recommend rollback or fixes
- Update migration plan

## Safety Features

1. **Comprehensive Analysis**: Analyzes all breaking changes upfront
2. **Incremental Approach**: Can migrate in phases
3. **Rollback Ready**: Tested rollback plan always available
4. **Dual-Write Support**: Run old and new in parallel
5. **Monitoring**: Continuous monitoring during migration
6. **Test Coverage**: Full test suite required

## Output Files

```
migration-output/
├── MIGRATION_SUMMARY.md      # Executive summary
├── BREAKING_CHANGES.md       # All breaking changes
├── IMPACT_ANALYSIS.md        # Codebase impact
├── MIGRATION_STRATEGY.md     # Chosen strategy
├── CHANGES_MADE.md           # All code changes
├── ROLLBACK_PLAN.md          # Rollback procedure
├── TESTING_RESULTS.md        # Test outcomes
├── PERFORMANCE_COMPARISON.md # Before/after metrics
└── TEAM_GUIDE.md             # Developer reference
```

## Integration with Other Plugins

- **After Research**: `/research` evaluates version → `/migrate` performs upgrade
- **With Test**: Can run tests during migration
- **Before Deploy**: Migrate in staging before production
- **With Monitor**: Monitor metrics during and after migration

## Notes

- **Planning is Critical**: 70% planning, 30% execution
- **Test Coverage Required**: Need tests to validate migration
- **Rollback is Essential**: Always have a rollback plan
- **Incremental is Safer**: For large systems, go incremental
- **Monitor Closely**: Watch for issues 24-48 hours post-migration

## Best Practices

1. **Analyze First**: Understand all breaking changes before starting
2. **Test in Staging**: Never migrate production without staging test
3. **Have Rollback Ready**: Test rollback procedure
4. **Monitor Metrics**: Watch error rates, performance, user reports
5. **Communicate**: Keep team informed throughout migration
6. **Document Everything**: Future migrations benefit from lessons learned

## Common Migration Patterns

The plugin supports:

**Framework Migrations**:
- React (any version)
- Vue 2→3
- Angular 1→2+
- Django 1.x→4.x
- Rails 5→7

**Database Migrations**:
- PostgreSQL
- MySQL
- MongoDB
- Redis

**Language Migrations**:
- Python 2→3
- PHP 7→8
- Node 14→20
- Java 8→17

**API Migrations**:
- REST API versioning
- GraphQL schema evolution
- gRPC updates
