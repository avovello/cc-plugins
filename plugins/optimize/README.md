# Optimize Plugin

Performance optimization through systematic profiling, bottleneck identification, and measurable speed improvements.

## Overview

The Optimize plugin provides a data-driven approach to performance optimization. Research shows that **80% of performance issues come from 20% of code**. This plugin helps you find and fix those critical bottlenecks systematically.

**Key Distinction**:
- **Optimize** improves speed and efficiency (this plugin)
- **Refactor** improves maintainability without changing performance
- **Bugfix** repairs broken behavior

## Problem It Solves

```
Current Challenge:
  "My API is slow" → ❌ Guessing what to optimize
         ↓
  Random optimizations (may not help)
         ↓
  Wasted effort, minimal improvement
```

```
With Optimize Plugin:
  Profile → Identify Bottlenecks → Plan → Optimize → Validate ✅
         ↓
  Data-driven, targeted optimizations
         ↓
  Measurable, significant improvements
```

## Use Cases

- **Slow API Endpoints**: Reduce response time from 450ms to 65ms (86% faster)
- **Database Performance**: Fix N+1 queries, add indexes (90-99% improvement)
- **Algorithm Optimization**: O(n²) → O(n) for 100-1000× speedup
- **Caching Strategy**: Add Redis/CDN caching (85-95% faster with cache hits)
- **Frontend Performance**: Reduce React render time from 3.2s to 0.4s (87% faster)
- **Load Testing**: Ensure performance holds under production load

## Usage

```bash
/optimize "improve performance of /api/users endpoint"
/optimize "optimize database queries in src/services/"
/optimize "reduce React component re-renders in dashboard"
/optimize "improve performance of findDuplicates function"
/optimize "add caching to user profile API"
/optimize                                    # Interactive mode
```

## Workflow

### 1. **Profile** Current Performance
- Establish baseline metrics
- Measure response times, throughput, resource usage
- Identify slow operations

### 2. **Identify** Bottlenecks
- Apply 80/20 rule to find critical bottlenecks
- Categorize by type (database, algorithm, caching, network)
- Prioritize by impact/effort ratio

### 3. **Plan** Optimizations
- Design optimization strategy for each bottleneck
- Estimate improvement potential
- Consider trade-offs

### 4. **Apply** Optimizations Incrementally
```
For each optimization:
  Benchmark → Apply → Benchmark → (Improved?) → Commit → Next
                                      ↓ No improvement
                                    Revert → Try Alternative
```

### 5. **Load Test** Under Realistic Conditions
- Verify improvements hold at scale
- Test under stress conditions
- Identify new bottlenecks

### 6. **Validate** Improvements
- Compare before/after metrics
- Verify all tests pass
- Confirm targets achieved

## Specialized Agents (7)

### 1. **performance-profiler**
**Focus**: Establish performance baseline

**Measures**:
- API response times (p50, p95, p99)
- Database query times
- CPU/memory usage
- Frontend render times

**Output**: Baseline metrics and slow operations report

### 2. **bottleneck-identifier**
**Focus**: Find the 20% of code causing 80% of delays

**Analyzes**:
- Database bottlenecks (N+1 queries, missing indexes)
- Algorithm complexity (O(n²) loops)
- Caching opportunities (repeated expensive operations)
- Network inefficiency (chatty APIs, large payloads)

**Output**: Prioritized bottleneck list with ROI scores

### 3. **query-optimizer**
**Focus**: Optimize database performance

**Techniques**:
- Fix N+1 queries with eager loading
- Add missing indexes
- Optimize JOIN operations
- Implement query caching
- Optimize pagination

**Impact**: 90-99% faster database operations

### 4. **cache-strategist**
**Focus**: Design and implement caching

**Strategies**:
- Redis for application caching
- CDN for static assets
- In-memory for reference data
- Query result caching

**Impact**: 80-95% faster for cache hits

### 5. **load-tester**
**Focus**: Validate performance under load

**Tests**:
- Normal load (sustained traffic)
- Peak load (high traffic periods)
- Stress test (find breaking point)
- Spike test (sudden traffic surge)

**Verifies**: Performance improvements hold at scale

### 6. **code-optimizer**
**Focus**: Optimize algorithms and data structures

**Techniques**:
- Reduce complexity (O(n²) → O(n))
- Better data structures (array → hash map)
- Eliminate redundant computations
- Optimize string operations
- Reduce memory allocations

**Impact**: 10-1000× faster for algorithmic improvements

### 7. **benchmark-validator**
**Focus**: Prove optimizations work

**Validates**:
- Before/after performance comparison
- Correctness preserved (all tests pass)
- Improvements consistent across runs
- No regressions in other areas

**Output**: Comprehensive benchmark validation report

## Examples

### Example 1: Optimize Slow API Endpoint

```bash
/optimize "improve performance of /api/users endpoint"
```

**Output**:
```
✅ Profiled /api/users endpoint (current: 450ms p95)
✅ Identified 3 bottlenecks:
   1. N+1 query (300ms) - HIGH IMPACT
   2. Missing index on user_roles (100ms) - MEDIUM IMPACT
   3. Eager loading all fields (50ms) - LOW IMPACT

✅ Step 1/3: Fix N+1 query with eager loading
   Before: 450ms → After: 150ms (67% faster) ✅

✅ Step 2/3: Add index on user_roles.user_id
   Before: 150ms → After: 80ms (47% faster) ✅

✅ Step 3/3: Implement field selection
   Before: 80ms → After: 65ms (19% faster) ✅

Overall improvement: 450ms → 65ms (86% faster) 🚀
All 87 tests pass ✅
```

### Example 2: Optimize Database Queries

```bash
/optimize "improve database query performance in src/services/"
```

**Output**:
```
✅ Analyzed 234 database queries
✅ Found 12 slow queries (>100ms)
✅ Identified optimization opportunities:
   - 5 missing indexes
   - 3 N+1 query patterns
   - 4 queries returning unnecessary data

✅ Step 1/12: Add index on orders.customer_id
   Query time: 450ms → 12ms (96% faster) ✅

✅ Step 2/12: Fix N+1 in getOrdersWithItems
   Query count: 1+N → 2 queries (reduced from 150 to 2) ✅
   Time: 800ms → 45ms (94% faster) ✅
...

Total queries optimized: 12
Average improvement: 88% faster
All 156 tests pass ✅
```

### Example 3: Optimize Algorithm

```bash
/optimize "improve performance of findDuplicates function"
```

**Output**:
```
✅ Analyzed findDuplicates algorithm
✅ Current complexity: O(n²) nested loops
✅ Current performance: 12.5s for 10,000 items

✅ Optimization: Replace nested loops with Set-based approach
   Complexity: O(n²) → O(n)
   Performance: 12.5s → 45ms (99.6% faster) 🚀

Benchmark results (10,000 items):
   Before: 12,500ms
   After: 45ms
   Improvement: 277× faster

All 34 tests pass ✅
```

## Safety Features

1. **Benchmark-Driven**: Every optimization is measured before/after
2. **Test-Verified**: All tests must pass after optimization
3. **Incremental**: Small changes that can be reverted
4. **Load-Tested**: Verify improvements hold at scale
5. **Automatic Rollback**: Revert if optimization makes things worse

## Output

```
optimize-output/
├── BASELINE_METRICS.md        # Initial performance
├── BOTTLENECK_ANALYSIS.md     # Identified bottlenecks
├── OPTIMIZATION_PLAN.md       # Step-by-step plan
├── OPTIMIZATION_SUMMARY.md    # What was optimized
├── IMPROVEMENTS.md            # Before/after metrics
├── BENCHMARKS.md              # Detailed benchmark results
├── LOAD_TEST_RESULTS.md       # Performance under load
└── COMMIT_MESSAGE.md          # Suggested commit message
```

## Configuration

Optional `.claude/optimize-config.yaml`:

```yaml
optimize:
  targets:
    api_response_time_ms: 200        # Target p95 response time
    page_load_time_ms: 2000          # Target page load
    database_query_ms: 50            # Target query time
    throughput_rps: 1000             # Requests per second

  thresholds:
    min_improvement_percent: 20      # Minimum improvement to accept
    max_regression_percent: 5        # Maximum acceptable regression

  benchmarking:
    warm_up_runs: 10                 # Warm-up iterations
    measurement_runs: 100            # Measurement iterations
    load_test_duration_seconds: 300  # Load test duration

  safety:
    require_tests_pass: true         # Require tests before/after
    auto_revert_on_regression: true  # Revert if performance degrades
```

## Related Plugins

- **QA Plugin**: Verify correctness after optimization (`/qa`, `/unit`, `/integration`)
- **After Audit**: `/audit` identifies slow operations → `/optimize` speeds them up

## Integration with Other Plugins

- **After Audit**: `/audit` identifies slow operations → `/optimize` speeds them up
- **After Review**: Address performance issues found in code review
- **Before Deploy**: Optimize before shipping to production
- **With Refactor**: Refactor for maintainability, then optimize for speed
- **Different from Refactor**: Refactor improves structure, Optimize improves speed

## Performance Targets

### API Response Times
| Target | Rating |
|--------|--------|
| < 100ms | Excellent ✅ |
| 100-300ms | Good ✓ |
| 300-1000ms | Acceptable ⚠️ |
| > 1000ms | Poor - Optimize 🔴 |

### Database Queries
| Target | Rating |
|--------|--------|
| < 10ms | Excellent ✅ |
| 10-50ms | Good ✓ |
| 50-100ms | Acceptable ⚠️ |
| > 100ms | Poor - Optimize 🔴 |

## Best Practices

1. **Profile first** - Don't guess where the bottlenecks are
2. **Focus on hotspots** - 80% of time is spent in 20% of code
3. **Measure everything** - Benchmark before and after every change
4. **Test under load** - Performance can change dramatically under stress
5. **Consider trade-offs** - Faster isn't always better if it uses 10× memory
6. **Cache strategically** - Right caching can provide 10-100× improvements
7. **Database first** - Database is often the biggest bottleneck

## Common Optimization Patterns

**Database**:
- Fix N+1 queries (90-99% improvement)
- Add indexes (90-99% improvement)
- Implement caching (85-95% improvement)

**Algorithms**:
- O(n²) → O(n log n) or O(n) (10-1000× improvement)
- Array → Hash map for lookups (10-100× improvement)

**Frontend**:
- Add React.memo (reduce re-renders by 90-99%)
- Implement virtualization (90-95% faster for long lists)
- Code splitting (40-70% faster initial load)

**Caching**:
- Redis caching (90-99% faster for cache hits)
- CDN for static assets (eliminates server requests)
- HTTP caching headers (browser caching)

## Statistics

- **Agents**: 7 specialized optimization agents
- **Typical Improvements**: 50-95% faster
- **Best Case**: 99%+ improvement (caching, algorithm optimization)
- **Industry Standard**: 80/20 rule - focus on critical bottlenecks

## Why This Plugin Exists

Research shows:
- **47% of users** expect pages to load in 2 seconds or less
- **40% abandon** a site that takes more than 3 seconds to load
- **1 second delay** = 7% reduction in conversions
- **Amazon**: Every 100ms of latency costs 1% of sales

Performance matters. This plugin makes optimization systematic, measurable, and safe.

---

**Priority**: P2 - HIGH
**Why**: Performance directly impacts user experience and business metrics
