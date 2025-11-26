# Migrate Plugin 🚀

Safe framework and database migrations with breaking change handling and rollback strategies.

## Overview

The Migrate plugin handles complex version upgrades that Refactor and Feature plugins don't address: framework upgrades, database migrations, and API version changes with breaking changes.

## Use Cases

- **Framework Upgrades**: React 17→18, Django 3→4, Laravel 9→10
- **Language Versions**: Python 3.8→3.12, PHP 8.0→8.3, Node.js 16→20
- **Database Migrations**: PostgreSQL 12→15, MySQL 5.7→8.0
- **API Versions**: REST API v1→v2, GraphQL schema evolution
- **Library Migrations**: moment→date-fns, lodash→native methods

## Usage

```bash
/migrate "Upgrade React 17 to 18"
/migrate "PostgreSQL 12 to 15"
/migrate "Python 3.8 to 3.11"
```

## Agents (7)

1. **compatibility-analyzer** - Analyze breaking changes
2. **migration-planner** - Plan migration strategy
3. **code-transformer** - Transform code
4. **dual-write-implementer** - Dual-write for databases
5. **migration-tester** - Test both versions
6. **rollback-planner** - Rollback strategy
7. **documentation-updater** - Update docs

## Migration Strategies

- **Big Bang**: Update all at once (simple)
- **Incremental**: Module by module (complex)
- **Dual-Write**: Write to both during transition (databases)

## Related Plugins

- **QA Plugin**: Critical for migration validation (`/qa`, `/e2e`, `/unit`, `/integration`)
  - Run full test suite on both old and new versions
  - E2E browser tests for UI framework migrations (React, Vue, Angular)
  - Integration tests for database migrations

## Priority

**P1 - HIGH** - Common operation for keeping tech stack modern
