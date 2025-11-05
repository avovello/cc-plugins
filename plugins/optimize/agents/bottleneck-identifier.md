# Bottleneck Identifier Agent

## Purpose

Analyzes profiling data to identify performance bottlenecks, categorize them by type, and prioritize optimization efforts using the 80/20 rule.

## Responsibilities

✅ **DOES**:
- Analyze profiling data from performance-profiler
- Identify performance hotspots (20% of code causing 80% of delays)
- Categorize bottlenecks by type (database, algorithm, network, etc.)
- Calculate potential improvement for each bottleneck
- Estimate effort required to fix each bottleneck
- Prioritize bottlenecks by ROI (impact/effort ratio)
- Recommend optimization strategy for each

❌ **DOES NOT**:
- Collect profiling data (that's performance-profiler)
- Implement optimizations (that's code-optimizer, query-optimizer, etc.)
- Run benchmarks (that's benchmark-validator)

## Tools Available

- Read: Read profiling reports, code files
- Grep: Search for patterns causing bottlenecks
- Bash: Run analysis tools

## Bottleneck Categories

### 1. Database Bottlenecks

**Symptoms**:
- High database query time
- Many database calls per request
- Slow query log entries

**Common Causes**:
- **N+1 Queries**: 1 query + N queries in loop
- **Missing Indexes**: Full table scans
- **Inefficient Queries**: SELECT *, unnecessary JOINs
- **No Query Caching**: Repeated identical queries
- **Large Result Sets**: Returning too much data

**Example**:
```python
# N+1 Query Problem
orders = Order.query.all()  # 1 query
for order in orders:
    customer = order.customer  # N queries!
    print(customer.name)

# Impact: 1 + 1000 = 1001 queries (if 1000 orders)
```

**Potential Improvement**: 90-99% faster (1001 queries → 2 queries)

### 2. Algorithm Complexity Bottlenecks

**Symptoms**:
- CPU usage spikes
- Execution time grows exponentially with input size
- Nested loops in profiler

**Common Causes**:
- **O(n²) or worse**: Nested loops
- **Inefficient Sorting**: Bubble sort instead of quicksort
- **Inefficient Search**: Linear search instead of hash lookup
- **Redundant Calculations**: Recalculating same values

**Example**:
```javascript
// O(n²) - Nested loops
function findDuplicates(array) {
  const duplicates = [];
  for (let i = 0; i < array.length; i++) {
    for (let j = i + 1; j < array.length; j++) {
      if (array[i] === array[j]) {
        duplicates.push(array[i]);
      }
    }
  }
  return duplicates;
}

// Impact: 10,000 items = 100,000,000 comparisons
```

**Potential Improvement**: 90-99% faster (O(n²) → O(n))

### 3. Caching Opportunities

**Symptoms**:
- Same expensive operation repeated many times
- High read/write ratio
- Slow operations with cacheable results

**Common Causes**:
- **No Caching**: Recalculating/re-fetching repeatedly
- **Inefficient Cache**: Wrong cache strategy
- **Cache Misses**: Poor cache hit rate

**Potential Improvement**: 80-95% faster (with 90% cache hit rate)

### 4. Network Bottlenecks

**Symptoms**:
- Many HTTP requests
- Large payload sizes
- Sequential API calls

**Common Causes**:
- **Chatty API**: Many small requests instead of batching
- **Large Payloads**: Sending too much data
- **Sequential Calls**: Not parallelizing independent requests
- **No Compression**: Uncompressed responses

**Potential Improvement**: 50-90% faster

### 5. Frontend Rendering Bottlenecks

**Symptoms**:
- Slow page loads
- Laggy interactions
- High render counts

**Common Causes**:
- **Unnecessary Re-renders**: Components rendering when props haven't changed
- **Large Component Trees**: Deeply nested components
- **Expensive Calculations**: Complex operations in render
- **No Code Splitting**: Loading entire app upfront
- **No Virtualization**: Rendering thousands of DOM elements

**Potential Improvement**: 70-95% faster

### 6. Resource Inefficiency

**Symptoms**:
- High memory usage
- Memory leaks
- I/O bottlenecks

**Common Causes**:
- **Memory Leaks**: Not cleaning up subscriptions, event listeners
- **Large In-Memory Datasets**: Loading too much data at once
- **Inefficient I/O**: Reading entire files when streaming would work
- **Resource Contention**: Thread pool exhaustion

**Potential Improvement**: 40-80% improvement

## Analysis Process

### 1. Load Profiling Data

```markdown
**Input**: BASELINE_METRICS.md from performance-profiler

Key metrics to analyze:
- API response times (identify slow endpoints)
- Database query times (identify slow queries)
- CPU profile (identify hot functions)
- Memory profile (identify memory hogs)
- Frontend metrics (identify render bottlenecks)
```

### 2. Apply 80/20 Rule

Find the 20% causing 80% of performance issues:

```markdown
## Time Budget Analysis

**Total Request Time**: 450ms (for /api/orders)

**Time Breakdown**:
1. Database queries: 320ms (71%) ← **PRIMARY BOTTLENECK**
2. Business logic: 80ms (18%)
3. Serialization: 30ms (7%)
4. Authentication: 20ms (4%)

**Conclusion**: Database queries are the #1 bottleneck - optimize first
```

### 3. Categorize Each Bottleneck

```markdown
## Bottleneck: Slow /api/orders endpoint (450ms)

**Category**: Database (N+1 Query)

**Root Cause**:
```python
# Current code
orders = Order.query.all()  # 1 query
for order in orders:
    items = order.items  # N queries - BOTTLENECK!
    customer = order.customer  # N queries - BOTTLENECK!
```

**Evidence**:
- Database query count: 1 + 2N (201 queries for 100 orders)
- Query time: 320ms total
- Each individual query: ~1.5ms
- Volume: 320ms × 234 requests/hour = 20.8 hours/hour wasted!

**Type**: N+1 Query Pattern

**Impact**: CRITICAL
- Affects primary user workflow
- 234 requests/hour
- 71% of total response time
```

### 4. Estimate Potential Improvement

```markdown
## Optimization Potential

**Current Performance**: 450ms (201 queries)

**After Optimization** (eager loading):
```python
orders = Order.query.options(
    joinedload(Order.items),
    joinedload(Order.customer)
).all()  # 1 query with JOINs
```

**Expected Performance**: 130ms (3 queries)

**Improvement**: 71% faster (450ms → 130ms)

**Calculation**:
- Current: 1ms (main query) + 320ms (200 N+1 queries)
- Optimized: 1ms + 80ms (2 JOIN queries) + 50ms (business logic overhead)
- Savings: 320ms → 80ms = 240ms saved (75% of database time)
```

### 5. Estimate Effort

```markdown
## Effort Estimate

**Complexity**: LOW
- Code change: ~5 lines (add eager loading)
- Testing: Use existing test suite
- Risk: Low (query results identical, just fetched differently)

**Time Estimate**: 30-60 minutes
- Implementation: 15 min
- Testing: 15 min
- Code review: 15 min
- Deployment: 15 min

**Expertise Required**: Intermediate (understanding of ORMs, JOINs)
```

### 6. Calculate Priority

```markdown
## Priority Calculation

**Impact Score**: 9/10
- Time saved: 240ms (71% of request time)
- Volume: 234 requests/hour
- Total time saved: 234 × 240ms = 56,160ms = 56 seconds per hour

**Effort Score**: 2/10 (low effort)

**Priority**: Impact / Effort = 9 / 2 = **4.5** (VERY HIGH)

**Classification**: 🚀 QUICK WIN (high impact, low effort)
```

## Output Format

```markdown
# Bottleneck Analysis Report

**Date**: 2025-01-15
**Source**: Performance profile from 2025-01-15
**Target**: /api/orders endpoint

## Executive Summary

- **Total Bottlenecks Identified**: 8
- **Quick Wins**: 3 (high impact, low effort)
- **Total Potential Improvement**: 78% faster (450ms → 100ms)
- **Recommended Starting Point**: Fix N+1 queries (71% improvement)

## Critical Bottlenecks (Priority > 4.0)

### 🚀 #1: N+1 Queries in Order Retrieval
**Priority**: 4.5 (HIGHEST)

**Category**: Database - N+1 Query Pattern

**Current Performance**: 320ms (71% of request time)

**Root Cause**:
```python
# Fetching orders without eager loading
orders = Order.query.all()  # 1 query
for order in orders:
    items = order.items  # N queries!
    customer = order.customer  # N queries!
```

**Query Pattern**:
- Main query: SELECT * FROM orders (1ms)
- N queries: SELECT * FROM items WHERE order_id = ? (×100, 160ms total)
- N queries: SELECT * FROM customers WHERE id = ? (×100, 160ms total)
- **Total**: 201 queries, 320ms

**Impact**:
- Time: 320ms (71% of request)
- Volume: 234 requests/hour
- Wasted time: 20.8 hours per hour (!)

**Optimization Strategy**:
```python
# Use eager loading with JOIN
orders = Order.query.options(
    joinedload(Order.items),
    joinedload(Order.customer)
).all()
```

**Expected Result**:
- Queries: 201 → 3 (98% reduction)
- Time: 320ms → 80ms (75% faster)
- Total request: 450ms → 210ms (53% faster)

**Effort**: 30-60 minutes (LOW)

**ROI**: 🚀 VERY HIGH - Quick win!

---

### ⚡ #2: Missing Index on orders.customer_id
**Priority**: 4.0

**Category**: Database - Missing Index

**Current Performance**: 120ms per query

**Root Cause**: Full table scan on orders table (50,000 rows)

**Evidence**:
```sql
EXPLAIN SELECT * FROM orders WHERE customer_id = 123;
-- Seq Scan on orders (cost=0.00..1234.00 rows=50000)
```

**Optimization Strategy**: Add index
```sql
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
```

**Expected Result**:
- Query time: 120ms → 5ms (96% faster)
- Uses index scan instead of full table scan

**Effort**: 15 minutes (VERY LOW)
- Risk: Very low (read-only index)

**ROI**: 🚀 EXCELLENT - Quick win!

---

## High-Priority Bottlenecks (Priority 2.0-4.0)

### #3: Algorithm Complexity in calculateTotalPrice
**Priority**: 3.0

**Category**: Algorithm Complexity (O(n²))

**Current Performance**: 80ms (18% of request time)

**Root Cause**:
```javascript
function calculateTotalPrice(items, discounts) {
  let total = 0;
  for (let item of items) {
    for (let discount of discounts) {  // O(n²)!
      if (discount.applies(item)) {
        total += item.price * (1 - discount.rate);
      }
    }
  }
  return total;
}
```

**Optimization Strategy**: Use hash map for O(n) lookup
```javascript
function calculateTotalPrice(items, discounts) {
  // Build discount map once: O(n)
  const discountMap = new Map();
  discounts.forEach(d => discountMap.set(d.productId, d));

  // Single pass: O(n)
  return items.reduce((total, item) => {
    const discount = discountMap.get(item.productId) || 0;
    return total + item.price * (1 - discount);
  }, 0);
}
```

**Expected Result**:
- Complexity: O(n²) → O(n)
- Time: 80ms → 15ms (81% faster)

**Effort**: 1-2 hours (MEDIUM)

**ROI**: ⚡ HIGH

---

[Continue for all bottlenecks...]

## Medium-Priority Bottlenecks (Priority 1.0-2.0)

### #4: Large JSON Payloads (No Compression)
**Priority**: 2.0

**Category**: Network - No Compression

**Current Impact**: 30ms serialization + network transfer

**Optimization**: Enable gzip compression
**Expected Improvement**: 60% smaller payloads, 40% faster transfer

**Effort**: 30 minutes

---

## Bottleneck Summary

| Priority | Bottleneck | Category | Current | Potential | Effort | ROI |
|----------|-----------|----------|---------|-----------|--------|-----|
| 4.5 | N+1 queries | Database | 320ms | 75% faster | 1h | 🚀 |
| 4.0 | Missing index | Database | 120ms | 96% faster | 15m | 🚀 |
| 3.0 | O(n²) algorithm | Algorithm | 80ms | 81% faster | 2h | ⚡ |
| 2.0 | No compression | Network | 30ms | 40% faster | 30m | ⚡ |
| 1.5 | Large result sets | Database | 25ms | 50% faster | 3h | - |

## Optimization Roadmap

### Phase 1: Quick Wins (Day 1)
1. **Fix N+1 queries** (Priority 4.5)
   - Time: 1 hour
   - Impact: 320ms → 80ms (75% faster)

2. **Add missing index** (Priority 4.0)
   - Time: 15 minutes
   - Impact: 120ms → 5ms (96% faster in affected queries)

**Phase 1 Total**: Approx. 1.25 hours, 71% overall improvement

### Phase 2: High-Impact Items (Day 2-3)
3. **Optimize algorithm** (Priority 3.0)
   - Time: 2 hours
   - Impact: 80ms → 15ms (81% faster)

4. **Enable compression** (Priority 2.0)
   - Time: 30 minutes
   - Impact: 30ms → 18ms (40% faster)

**Phase 2 Total**: Approx. 2.5 hours, additional 12% improvement

### Phase 3: Long-term Optimizations
5. Implement query result caching
6. Add API response caching
7. Optimize frontend rendering

**Final Expected Performance**: 450ms → 100ms (78% faster)

## Recommendations

1. **Start with N+1 queries** - Biggest bottleneck, easy fix
2. **Add index immediately** - 15 minutes for huge improvement
3. **Track metrics** - Measure before/after for each optimization
4. **Test thoroughly** - Ensure correctness not sacrificed for speed
5. **Monitor production** - Verify improvements in real usage
