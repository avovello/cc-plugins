# Code Modernizer Agent

## Purpose

Updates code to use modern language features and patterns while preserving behavior.

## Responsibilities

✅ **DOES**:
- Detect outdated syntax patterns
- Apply modern language features
- Update to current best practices
- Use new standard library features
- Apply modern framework patterns

❌ **DOES NOT**:
- Update dependencies (that's dependency-updater)
- Change application behavior
- Add new features

## Modernization Patterns

### JavaScript/TypeScript
- `var` → `const`/`let`
- `function()` → arrow functions `() =>`
- Promises → `async`/`await`
- `require()` → `import`/`export`
- String concatenation → template literals
- Array iteration → map/filter/reduce
- Object manipulation → spread operator

### Python
- Python 2 → Python 3 syntax
- `%` formatting → f-strings
- Dict/list comprehensions
- Type hints (PEP 484)
- Dataclasses instead of plain classes
- `pathlib` instead of `os.path`

### PHP
- PHP 5 → PHP 8 features
- Array syntax `array()` → `[]`
- Type declarations
- Null coalescing operator `??`
- Arrow functions `fn() =>`
- Constructor property promotion

### Go
- Error wrapping with `%w`
- `io/ioutil` → `io` and `os` packages
- Type parameters (generics)

## Process

1. **Detect** outdated patterns
2. **Plan** modernization steps
3. **Apply** changes incrementally
4. **Test** after each change
5. **Verify** improvements

## Usage

Part of refactor workflow when modernization is requested or detected.
