---
description: Performance optimization through systematic profiling, bottleneck identification, and speed improvements
---

# Optimize Command

**Purpose**: Performance optimization through systematic profiling, bottleneck identification, and measurable speed improvements

## Overview

The optimize command addresses performance bottlenecks through data-driven optimization. Research shows that **80% of performance issues come from 20% of code**, making targeted optimization critical.

**Key Distinction**: Optimize improves speed and efficiency, while Refactor improves maintainability without changing performance, and Bugfix repairs broken behavior.

## Workflow

### Step 1: Validate Target

1. **Check target exists**
   - If specific file/endpoint provided: verify it exists
   - If performance goal provided: identify optimization scope
   - If no target: offer to analyze recent performance metrics

2. **Determine optimization scope**
   - API endpoint optimization
   - Database query optimization
   - Frontend rendering optimization
   - Algorithm optimization
   - Resource usage optimization (memory, CPU, I/O)

3. **Identify project type**
   - Language (JavaScript, Python, PHP, Go, Java, etc.)
   - Framework (React, Django, Laravel, Express, etc.)
   - Database (PostgreSQL, MySQL, MongoDB, Redis)
   - Infrastructure (serverless, containers, VMs)

### Step 2: Profile Current Performance

Run **performance-profiler** agent to:
- Establish performance baseline
- Measure current response times, throughput, resource usage
- Identify slow operations (>100ms API calls, >1s page loads)
- Measure database query times
- Profile CPU/memory usage
- Document current performance metrics

**Output**:
```
optimize-analysis/
├── BASELINE_METRICS.md        # Current performance numbers
├── PROFILING_DATA.md          # Detailed profiling results
├── SLOW_OPERATIONS.md         # Operations exceeding thresholds
└── RESOURCE_USAGE.md          # CPU, memory, I/O usage
```

### Step 3: Identify Bottlenecks

Run **bottleneck-identifier** agent to:
- Analyze profiling data
- Identify performance hotspots (80/20 rule)
- Categorize bottlenecks by type:
  - Algorithm complexity (O(n²) → O(n log n))
  - Database queries (N+1, missing indexes)
  - Network calls (excessive API calls, no caching)
  - Resource inefficiency (memory leaks, CPU spikes)
  - Frontend rendering (unnecessary re-renders)
- Estimate potential improvement for each
- Prioritize by impact

**Output**: Prioritized list of bottlenecks with estimated improvements

### Step 4: Create Optimization Plan

Run appropriate specialized agents based on bottleneck types:

**Database Issues** → **query-optimizer** agent:
- Analyze slow queries
- Design indexes
- Optimize query structure
- Implement query caching

**Caching Opportunities** → **cache-strategist** agent:
- Identify cacheable data
- Design cache strategy (Redis, CDN, in-memory)
- Determine cache invalidation approach
- Plan cache warming

**Algorithm Issues** → **code-optimizer** agent:
- Analyze algorithm complexity
- Design more efficient algorithms
- Plan data structure improvements
- Optimize hot code paths

**Output**: `OPTIMIZATION_PLAN.md` with step-by-step approach

### Step 5: Review Plan (Optional)

Present plan to user:
- Expected improvements (e.g., "Reduce API response time from 450ms to 80ms")
- Specific optimizations
- Risks and trade-offs
- Implementation effort

Wait for approval before proceeding.

### Step 6: Apply Optimizations Incrementally

For each optimization:

1. **Benchmark Before**
   - Measure current performance
   - Run performance tests
   - Document baseline

2. **Apply Optimization**
   - Make focused change
   - Keep changes atomic

3. **Benchmark After**
   - Measure new performance
   - Compare to baseline
   - Verify improvement achieved

4. **Run Tests**
   - Ensure all tests pass
   - Verify no behavior changes
   - Check for regressions

5. **If Improved**: ✅
   - Document improvement
   - Commit change
   - Move to next optimization

6. **If No Improvement or Regression**: ❌
   - Revert change
   - Analyze why it didn't help
   - Try alternative approach

### Step 7: Load Test

Run **load-tester** agent to:
- Simulate realistic load
- Test under stress conditions
- Verify improvements hold at scale
- Identify new bottlenecks under load
- Measure key metrics:
  - Response time (p50, p95, p99)
  - Throughput (requests/second)
  - Error rate
  - Resource utilization

**Success Criteria**:
- ✅ Performance targets met (e.g., p95 < 200ms)
- ✅ No errors under normal load
- ✅ Graceful degradation under stress
- ✅ Resource usage within limits

### Step 8: Validate Improvements

Run **benchmark-validator** agent to:
- Re-run all performance benchmarks
- Compare before/after metrics
- Verify targets achieved
- Check for side effects or regressions
- Validate improvements persist

**Success Criteria**:
- ✅ Performance improved by target % (e.g., 50% faster)
- ✅ All tests pass
- ✅ No behavior changes
- ✅ No new bottlenecks introduced

### Step 9: Document Optimizations

Generate documentation:
```
optimize-output/
├── OPTIMIZATION_SUMMARY.md    # What was optimized and why
├── IMPROVEMENTS.md            # Before/after metrics
├── BENCHMARKS.md              # Detailed benchmark results
├── COMMIT_MESSAGE.md          # Suggested commit message
└── MONITORING_RECOMMENDATIONS.md  # What to monitor going forward
```

### Step 10: Create Commit (Optional)

If requested, create git commit:
- Use suggested commit message
- Mark as "perf:" in conventional commits format
- Include before/after metrics in commit body

## Optimization Loop

```
For each optimization:
  Benchmark → Apply → Benchmark → (Improved?) → Commit → Next
                                      ↓ No improvement
                                    Revert → Analyze → Try Alternative
```

**Max Iterations**: 20 optimizations per session
**Safety**: Each optimization is benchmarked and tested

## Performance Targets

### API Response Times
| Target | Rating |
|--------|--------|
| < 100ms | Excellent ✅ |
| 100-300ms | Good ✓ |
| 300-1000ms | Acceptable ⚠️ |
| > 1000ms | Poor - Optimize 🔴 |

### Page Load Times
| Target | Rating |
|--------|--------|
| < 1s | Excellent ✅ |
| 1-3s | Good ✓ |
| 3-5s | Acceptable ⚠️ |
| > 5s | Poor - Optimize 🔴 |

### Database Queries
| Target | Rating |
|--------|--------|
| < 10ms | Excellent ✅ |
| 10-50ms | Good ✓ |
| 50-100ms | Acceptable ⚠️ |
| > 100ms | Poor - Optimize 🔴 |

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

## Usage Examples

### Example 1: Optimize Slow API Endpoint

```bash
/optimize "improve performance of /api/users endpoint"

# Output:
# ✅ Profiled /api/users endpoint (current: 450ms p95)
# ✅ Identified 3 bottlenecks:
#    1. N+1 query (300ms) - HIGH IMPACT
#    2. Missing index on user_roles (100ms) - MEDIUM IMPACT
#    3. Eager loading all fields (50ms) - LOW IMPACT
#
# ✅ Step 1/3: Fix N+1 query with eager loading
#    Before: 450ms → After: 150ms (67% faster) ✅
#
# ✅ Step 2/3: Add index on user_roles.user_id
#    Before: 150ms → After: 80ms (47% faster) ✅
#
# ✅ Step 3/3: Implement field selection
#    Before: 80ms → After: 65ms (19% faster) ✅
#
# Overall improvement: 450ms → 65ms (86% faster) 🚀
# All 87 tests pass ✅
```

### Example 2: Optimize Database Queries

```bash
/optimize "improve database query performance in src/services/"

# Output:
# ✅ Analyzed 234 database queries
# ✅ Found 12 slow queries (>100ms)
# ✅ Identified optimization opportunities:
#    - 5 missing indexes
#    - 3 N+1 query patterns
#    - 4 queries returning unnecessary data
#
# ✅ Step 1/12: Add index on orders.customer_id
#    Query time: 450ms → 12ms (96% faster) ✅
#
# ✅ Step 2/12: Fix N+1 in getOrdersWithItems
#    Query count: 1+N → 2 queries (reduced from 150 to 2) ✅
#    Time: 800ms → 45ms (94% faster) ✅
# ...
#
# Total queries optimized: 12
# Average improvement: 88% faster
# All 156 tests pass ✅
```

### Example 3: Optimize Frontend Rendering

```bash
/optimize "reduce React component re-renders in dashboard"

# Output:
# ✅ Profiled React dashboard (current: 3.2s initial render)
# ✅ Identified performance issues:
#    - Unnecessary re-renders: 847 renders for 12 components
#    - Large component tree: 45 nested levels
#    - Missing memoization: 15 expensive calculations
#
# ✅ Step 1/5: Add React.memo to list components
#    Renders: 847 → 23 (97% reduction) ✅
#
# ✅ Step 2/5: Use useMemo for expensive calculations
#    Calculation time: 1.2s → 5ms (99.6% faster) ✅
#
# ✅ Step 3/5: Implement virtual scrolling for long lists
#    Render time: 1.8s → 120ms (93% faster) ✅
# ...
#
# Overall: 3.2s → 0.4s initial render (87.5% faster) 🚀
# Lighthouse score: 45 → 92 ✅
```

### Example 4: Optimize Algorithm Complexity

```bash
/optimize "improve performance of findDuplicates function"

# Output:
# ✅ Analyzed findDuplicates algorithm
# ✅ Current complexity: O(n²) nested loops
# ✅ Current performance: 12.5s for 10,000 items
#
# ✅ Optimization: Replace nested loops with Set-based approach
#    Complexity: O(n²) → O(n)
#    Performance: 12.5s → 45ms (99.6% faster) 🚀
#
# Benchmark results (10,000 items):
#    Before: 12,500ms
#    After: 45ms
#    Improvement: 277x faster
#
# All 34 tests pass ✅
```

### Example 5: Optimize with Caching

```bash
/optimize "add caching to user profile API"

# Output:
# ✅ Analyzed /api/profile endpoint (current: 250ms p95)
# ✅ Identified caching opportunity:
#    - User data changes infrequently
#    - High read/write ratio (1000:1)
#    - Cache hit rate potential: 95%
#
# ✅ Designed cache strategy:
#    - Redis cache with 5-minute TTL
#    - Cache warming for active users
#    - Invalidation on user updates
#
# ✅ Implemented Redis caching
#    Cache hit: 5ms
#    Cache miss: 250ms (writes to cache)
#    Expected p95: 25ms (90% faster)
#
# ✅ Load test results:
#    With 95% cache hit rate: 25ms p95 ✅
#    Throughput: 500 rps → 5,000 rps (10x) 🚀
#
# All 67 tests pass ✅
```

## Quality Gates

Before completing optimization:

- ✅ Performance improved by minimum threshold (e.g., 20%)
- ✅ All tests pass
- ✅ No behavior changes
- ✅ No new errors or exceptions
- ✅ Improvements verified under load
- ✅ No unacceptable trade-offs (e.g., excessive memory usage)

If any gate fails:
- Report issue
- Offer to revert changes
- Suggest alternative approaches

## Safety Features

1. **Benchmark-Driven**: Every optimization is measured before/after
2. **Test-Verified**: All tests must pass
3. **Incremental**: Small changes that can be reverted
4. **Load-Tested**: Verify improvements hold at scale
5. **Automatic Rollback**: Revert if optimization makes things worse

## Output Files

```
optimize-output/
├── BASELINE_METRICS.md        # Initial performance
├── OPTIMIZATION_PLAN.md       # Step-by-step plan
├── OPTIMIZATION_SUMMARY.md    # What was optimized
├── IMPROVEMENTS.md            # Before/after metrics
├── BENCHMARKS.md              # Detailed benchmark results
├── LOAD_TEST_RESULTS.md       # Performance under load
├── COMMIT_MESSAGE.md          # Suggested commit message
└── MONITORING_RECOMMENDATIONS.md  # What to monitor
```

## Integration with Other Plugins

- **After Audit**: `/audit` identifies slow operations → `/optimize` speeds them up
- **After Review**: Address performance issues found in code review
- **Before Deploy**: Optimize before shipping to production
- **With Refactor**: Refactor for maintainability, then optimize for speed
- **Different from Refactor**: Refactor improves structure, Optimize improves speed

## Notes

- **Measure First**: Always profile before optimizing
- **80/20 Rule**: Focus on the biggest bottlenecks
- **Test Thoroughly**: Ensure correctness isn't sacrificed for speed
- **Trade-offs**: Consider memory vs CPU vs complexity trade-offs
- **Premature Optimization**: Don't optimize until you have performance issues
- **Real Data**: Use production-like data and load for testing

## Best Practices

1. **Profile first** - Don't guess where the bottlenecks are
2. **Focus on hotspots** - 80% of time is spent in 20% of code
3. **Measure everything** - Benchmark before and after every change
4. **Test under load** - Performance can change dramatically under stress
5. **Consider trade-offs** - Faster isn't always better if it uses 10x memory
6. **Cache strategically** - Right caching can provide 10-100x improvements
7. **Database first** - Database is often the biggest bottleneck

## Common Optimization Patterns

The plugin supports these optimization strategies:

**Database**:
- Add indexes
- Fix N+1 queries
- Optimize query structure
- Implement query caching
- Add connection pooling

**Algorithms**:
- Reduce complexity (O(n²) → O(n log n))
- Use better data structures (array → hash map)
- Eliminate redundant work
- Optimize hot paths

**Caching**:
- Application caching (Redis, Memcached)
- Database query caching
- CDN for static assets
- HTTP caching headers

**Frontend**:
- Code splitting
- Lazy loading
- Memoization
- Virtual scrolling
- Debouncing/throttling

**Infrastructure**:
- Load balancing
- Horizontal scaling
- Resource optimization
- Compression
