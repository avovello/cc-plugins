# Integration Testing Skill

Integration testing capability for testing component interactions, APIs, and databases.

## When to Use

- Testing API endpoints
- Testing database operations
- Testing service integrations
- Testing component interactions
- Testing external service connections

## Capabilities

### Test Types

| Type | Description | Tools |
|------|-------------|-------|
| API Integration | HTTP endpoints | supertest, requests |
| Database Integration | CRUD operations | Test DB, migrations |
| Service Integration | External services | Mocked services |
| Component Integration | Module interactions | Real dependencies |

### Framework Support

```bash
# JavaScript/TypeScript
npm test -- tests/integration/
npx vitest run tests/integration/

# Python
pytest tests/integration/

# PHP
./vendor/bin/phpunit --testsuite Integration
```

## Workflow

1. **Setup** - Prepare test database/services
2. **Generate** - Use `test-writer` for integration tests
3. **Execute** - Use `test-runner` to run tests
4. **Cleanup** - Reset state after tests

## Test Patterns

### API Testing

```typescript
import request from 'supertest';
import { app } from '@/app';

describe('POST /api/users', () => {
  it('should create user', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({ name: 'Test', email: 'test@example.com' })
      .expect(201);

    expect(response.body).toHaveProperty('id');
  });
});
```

### Database Testing

```typescript
describe('UserRepository', () => {
  beforeEach(async () => {
    await db.migrate.latest();
    await db.seed.run();
  });

  afterEach(async () => {
    await db('users').truncate();
  });

  it('should find user by email', async () => {
    const user = await repository.findByEmail('test@example.com');
    expect(user).toBeDefined();
  });
});
```

### Service Mocking

```typescript
// Mock external payment service
jest.mock('@/services/payment', () => ({
  processPayment: jest.fn().mockResolvedValue({ success: true })
}));
```

## Quick Reference

### HTTP Methods

```typescript
// supertest
request(app).get('/api/users')
request(app).post('/api/users').send(data)
request(app).put('/api/users/1').send(data)
request(app).delete('/api/users/1')
```

### Status Assertions

```typescript
.expect(200)  // OK
.expect(201)  // Created
.expect(400)  // Bad Request
.expect(401)  // Unauthorized
.expect(404)  // Not Found
.expect(500)  // Server Error
```

## Test Database Setup

### Options

1. **In-memory** - SQLite for fast tests
2. **Docker** - Containerized test DB
3. **Transactions** - Rollback after each test
4. **Fixtures** - Seed data for tests

### Example Configuration

```typescript
// vitest.config.ts
export default {
  test: {
    setupFiles: ['./tests/setup.ts'],
    globalSetup: './tests/globalSetup.ts',
  }
}
```

## Integration with QA Plugin

- **Agent**: `test-writer` - Generate integration tests
- **Agent**: `test-runner` - Execute tests
- **Command**: `/integration` - Run integration test workflow
