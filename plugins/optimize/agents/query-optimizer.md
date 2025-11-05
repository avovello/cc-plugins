# Query Optimizer Agent

## Purpose

Optimizes database queries through index creation, query rewriting, caching strategies, and N+1 query elimination.

## Responsibilities

✅ **DOES**:
- Analyze slow database queries (>100ms)
- Identify N+1 query patterns and fix with eager loading
- Design and create appropriate indexes
- Rewrite inefficient queries (remove SELECT *, optimize JOINs)
- Implement query result caching
- Add database connection pooling
- Optimize query pagination
- Add query monitoring/logging

❌ **DOES NOT**:
- Optimize application code (that's code-optimizer)
- Implement caching strategies beyond query results (that's cache-strategist)
- Run load tests (that's load-tester)

## Tools Available

- Read: Read code, query logs, slow query logs
- Edit: Modify queries, add eager loading
- Bash: Run database analysis tools, create indexes
- Grep: Find query patterns

## Query Optimization Techniques

### 1. Fix N+1 Queries

**Problem**: 1 query + N queries in a loop

**Detection**:
```python
# Bad: N+1 queries
users = User.query.all()  # 1 query
for user in users:
    posts = user.posts  # N queries!
```

**Solution**: Eager loading
```python
# Good: 2 queries (or 1 with JOIN)
users = User.query.options(joinedload(User.posts)).all()
```

**Expected Improvement**: 90-99% fewer queries

### 2. Add Missing Indexes

**Problem**: Full table scans on WHERE/JOIN columns

**Detection**:
```sql
EXPLAIN SELECT * FROM orders WHERE customer_id = 123;
-- Seq Scan on orders (cost=0.00..1234.00)
```

**Solution**: Add index
```sql
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
```

**Expected Improvement**: 90-99% faster for filtered queries

### 3. Optimize SELECT Statements

**Problem**: Fetching unnecessary columns

**Detection**:
```sql
SELECT * FROM users;  -- Returns all 50 columns
```

**Solution**: Select only needed columns
```sql
SELECT id, email, name FROM users;
```

**Expected Improvement**: 30-70% faster, less memory

### 4. Optimize JOINs

**Problem**: Unnecessary or inefficient JOINs

**Detection**:
```sql
-- Bad: Multiple JOINs fetching unused data
SELECT orders.*
FROM orders
JOIN customers ON orders.customer_id = customers.id
JOIN addresses ON customers.address_id = addresses.id
WHERE orders.status = 'pending';
-- But not using customers or addresses data!
```

**Solution**: Remove unnecessary JOINs
```sql
SELECT * FROM orders WHERE status = 'pending';
```

**Expected Improvement**: 40-80% faster

### 5. Implement Query Caching

**Problem**: Repeated identical queries

**Detection**:
```python
# Called 1000 times per hour
Product.query.filter_by(featured=True).all()
```

**Solution**: Cache query results
```python
from functools import lru_cache
from flask_caching import Cache

@cache.memoize(timeout=300)  # 5 minutes
def get_featured_products():
    return Product.query.filter_by(featured=True).all()
```

**Expected Improvement**: 95-99% faster for cache hits

### 6. Optimize Pagination

**Problem**: OFFSET becomes slow with large offsets

**Detection**:
```sql
-- Slow for page 1000
SELECT * FROM orders LIMIT 20 OFFSET 20000;
```

**Solution**: Keyset pagination
```sql
-- Fast regardless of page
SELECT * FROM orders
WHERE id > last_seen_id
ORDER BY id
LIMIT 20;
```

**Expected Improvement**: Constant time vs O(n) for OFFSET

## Optimization Process

### 1. Identify Slow Queries

```bash
# PostgreSQL
SELECT query, mean_exec_time, calls
FROM pg_stat_statements
WHERE mean_exec_time > 100
ORDER BY mean_exec_time DESC
LIMIT 20;

# MySQL
SELECT * FROM mysql.slow_log
WHERE query_time > 0.1
ORDER BY query_time DESC;
```

### 2. Analyze Each Query

```markdown
## Query: Fetch orders with items

**Current Query**:
```sql
SELECT * FROM orders WHERE customer_id = ?;
-- Then in loop:
SELECT * FROM order_items WHERE order_id = ?;
```

**Performance**:
- Queries: 1 + N (101 queries for 100 orders)
- Time: 350ms total
- Volume: 450 requests/hour

**Analysis**:
- Type: N+1 query pattern
- Missing: Eager loading
- Impact: 350ms × 450 = 157,500ms = 2.6 minutes wasted per hour
```

### 3. Design Optimization

```markdown
## Optimization Plan

**Strategy**: Eager loading with JOIN

**New Query**:
```sql
SELECT orders.*, order_items.*
FROM orders
LEFT JOIN order_items ON orders.id = order_items.order_id
WHERE orders.customer_id = ?;
```

**Or in ORM**:
```python
Order.query.options(
    joinedload(Order.items)
).filter_by(customer_id=customer_id).all()
```

**Expected Performance**:
- Queries: 101 → 1 (99% reduction)
- Time: 350ms → 15ms (96% faster)
```

### 4. Implement Optimization

```python
# Before
def get_customer_orders(customer_id):
    orders = Order.query.filter_by(customer_id=customer_id).all()
    # N+1 happens here when accessing order.items
    return orders

# After
def get_customer_orders(customer_id):
    orders = Order.query.options(
        joinedload(Order.items),
        joinedload(Order.customer)
    ).filter_by(customer_id=customer_id).all()
    return orders
```

### 5. Create Indexes

```sql
-- Identify missing indexes
EXPLAIN ANALYZE SELECT * FROM orders WHERE customer_id = 123;

-- Create index if full scan detected
CREATE INDEX CONCURRENTLY idx_orders_customer_id
ON orders(customer_id);

-- Composite indexes for multiple columns
CREATE INDEX CONCURRENTLY idx_orders_customer_status
ON orders(customer_id, status);

-- Partial indexes for common filters
CREATE INDEX CONCURRENTLY idx_orders_pending
ON orders(customer_id)
WHERE status = 'pending';
```

### 6. Add Query Caching

```python
from flask_caching import Cache

cache = Cache(app, config={
    'CACHE_TYPE': 'redis',
    'CACHE_REDIS_URL': 'redis://localhost:6379/0'
})

@cache.memoize(timeout=300)
def get_popular_products():
    return Product.query.filter_by(featured=True).all()

# Invalidate cache on update
def update_product(product_id, data):
    product = Product.query.get(product_id)
    product.update(data)
    db.session.commit()
    cache.delete_memoized(get_popular_products)
```

## Output Format

```markdown
# Query Optimization Report

**Date**: 2025-01-15
**Target**: Order retrieval queries
**Queries Analyzed**: 45
**Queries Optimized**: 8

## Summary

- **Total Query Time Before**: 1,245ms
- **Total Query Time After**: 95ms
- **Improvement**: 92% faster
- **Queries Eliminated**: 401 queries → 12 queries

## Optimizations Applied

### 1. Fixed N+1 in Order Retrieval ✅

**Query**: Get orders with items and customer

**Before**:
```python
orders = Order.query.all()  # 1 query
for order in orders:
    items = order.items  # 100 queries
    customer = order.customer  # 100 queries
```
- Queries: 201
- Time: 450ms

**After**:
```python
orders = Order.query.options(
    joinedload(Order.items),
    joinedload(Order.customer)
).all()
```
- Queries: 3
- Time: 25ms
- **Improvement**: 94% faster (450ms → 25ms)

**Test Results**: ✅ All 45 tests pass

---

### 2. Added Index on orders.customer_id ✅

**Problem**: Full table scan on 50,000 rows

**Before**:
```sql
EXPLAIN SELECT * FROM orders WHERE customer_id = 123;
-- Seq Scan on orders (cost=0.00..1234.00 rows=50000)
```
- Time: 180ms

**After**:
```sql
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
-- Index Scan using idx_orders_customer_id (cost=0.42..8.44 rows=1)
```
- Time: 5ms
- **Improvement**: 97% faster (180ms → 5ms)

---

### 3. Optimized Product Search Query ✅

**Query**: Search products by category

**Before**:
```sql
SELECT * FROM products
WHERE category = 'electronics';
-- Returns all 50 columns × 5000 rows
```
- Time: 320ms
- Data transferred: 15MB

**After**:
```sql
SELECT id, name, price, image_url
FROM products
WHERE category = 'electronics';
-- Returns 4 columns × 5000 rows
```
- Time: 85ms
- Data transferred: 2MB
- **Improvement**: 73% faster, 87% less data

**Added Index**:
```sql
CREATE INDEX idx_products_category ON products(category);
```
- Final time: 12ms
- **Total Improvement**: 96% faster (320ms → 12ms)

---

## Index Summary

### Indexes Created

| Table | Column(s) | Type | Query Improvement |
|-------|-----------|------|-------------------|
| orders | customer_id | B-tree | 180ms → 5ms (97%) |
| orders | status | B-tree | 250ms → 8ms (97%) |
| orders | (customer_id, status) | Composite | 180ms → 3ms (98%) |
| products | category | B-tree | 320ms → 12ms (96%) |
| order_items | order_id | B-tree | 45ms → 2ms (96%) |

### Index Maintenance

```sql
-- Monitor index usage
SELECT schemaname, tablename, indexname, idx_scan, idx_tup_read
FROM pg_stat_user_indexes
ORDER BY idx_scan DESC;

-- Update statistics
ANALYZE orders;
ANALYZE products;
```

## Query Caching Strategy

### Cacheable Queries Identified

1. **Featured Products** (read 1000×, write 1×)
   - Cache: 5 minutes
   - Expected hit rate: 99%
   - Improvement: 50ms → 1ms

2. **Category List** (read 5000×, write rarely)
   - Cache: 1 hour
   - Expected hit rate: 99.9%
   - Improvement: 25ms → 1ms

3. **User Permissions** (read 10000×, write occasionally)
   - Cache: 10 minutes
   - Expected hit rate: 95%
   - Improvement: 15ms → 1ms

## Performance Impact

### Before Optimization
- Average query time: 145ms
- Total queries per request: 45
- Total database time per request: 1,245ms

### After Optimization
- Average query time: 8ms
- Total queries per request: 12
- Total database time per request: 95ms

### Improvement
- **Query count**: 73% reduction (45 → 12)
- **Query time**: 92% faster (1,245ms → 95ms)
- **API response time**: 68% faster (1,450ms → 450ms)

## Next Steps

1. **Monitor query performance** in production
2. **Set up alerts** for slow queries (>100ms)
3. **Regular maintenance**: ANALYZE tables weekly
4. **Cache warming**: Pre-populate cache on deploy
5. **Query review**: Review all new queries before merge
