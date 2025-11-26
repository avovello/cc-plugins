# Unit Testing Skill

Unit testing capability for isolated function and component testing.

## When to Use

- Testing individual functions
- Testing class methods
- Testing pure logic
- Testing with mocked dependencies
- Fast feedback during development

## Capabilities

### Framework Support

| Language | Framework | Config File |
|----------|-----------|-------------|
| JavaScript/TypeScript | Jest | jest.config.* |
| JavaScript/TypeScript | Vitest | vitest.config.* |
| Python | pytest | pytest.ini, pyproject.toml |
| PHP | PHPUnit | phpunit.xml |

### Test Execution

```bash
# JavaScript/TypeScript (Jest)
npm test -- tests/unit/
npm test -- --coverage

# JavaScript/TypeScript (Vitest)
npx vitest run tests/unit/
npx vitest run --coverage

# Python (pytest)
pytest tests/unit/
pytest --cov=src

# PHP (PHPUnit)
./vendor/bin/phpunit tests/Unit/
```

## Workflow

1. **Analyze** - Read source code to understand behavior
2. **Generate** - Use `test-writer` to create unit tests
3. **Execute** - Use `test-runner` to run tests
4. **Coverage** - Review coverage reports

## Test Structure

### Arrange-Act-Assert

```typescript
describe('Calculator', () => {
  it('should add two numbers', () => {
    // Arrange
    const a = 2, b = 3;

    // Act
    const result = add(a, b);

    // Assert
    expect(result).toBe(5);
  });
});
```

### Mocking Dependencies

```typescript
// Mock external dependency
const mockRepository = {
  findById: jest.fn().mockResolvedValue({ id: 1, name: 'Test' })
};

const service = new UserService(mockRepository);
```

## Quick Reference

### Common Assertions

```javascript
// Jest/Vitest
expect(value).toBe(expected)
expect(value).toEqual(expected)
expect(fn).toThrow(Error)
expect(array).toContain(item)
expect(mock).toHaveBeenCalledWith(args)
```

```python
# pytest
assert value == expected
assert item in collection
with pytest.raises(ValueError):
    function()
```

## Coverage Targets

| Type | Target |
|------|--------|
| Statements | 80%+ |
| Branches | 75%+ |
| Functions | 80%+ |
| Lines | 80%+ |

## Integration with QA Plugin

- **Agent**: `test-writer` - Generate unit tests
- **Agent**: `test-runner` - Execute tests with coverage
- **Command**: `/unit` - Run unit test workflow
