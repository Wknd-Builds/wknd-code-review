# Prisma And Postgres Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- Prisma documentation: https://www.prisma.io/docs
- Prisma Migrate documentation: https://www.prisma.io/docs/orm/prisma-migrate
- Prisma transactions documentation: https://www.prisma.io/docs/orm/prisma-client/queries/transactions
- PostgreSQL documentation: https://www.postgresql.org/docs/current/

Use this reference when reviewing Prisma schema changes, Postgres migrations, query shape, transactions, data backfills, deploy ordering, rollback safety, or production database behavior.

## Review Checklist

- Compare schema, generated client assumptions, and migration SQL for drift or unsupported manual edits.
- Treat destructive changes as high risk: drops, renames, type narrowing, `NOT NULL` additions, default changes, uniqueness changes, enum changes, and cascade behavior.
- Check deploy order for expand/backfill/contract sequencing when application code and database shape cannot change atomically.
- Require backfills or staged defaults before adding required columns or constraints to existing tables.
- Verify constraints encode product invariants: foreign keys, uniqueness, checks, `ON DELETE` behavior, nullable fields, and ownership/tenant relationships.
- Check indexes for new filters, sorts, joins, uniqueness, pagination paths, and foreign keys that will be queried at scale.
- Review query shape for N+1 behavior, unbounded includes, expensive relation loading, large selects, missing pagination, and offset pagination at large scale.
- Prefer cursor or keyset pagination where result sets can grow; confirm deterministic ordering.
- Use transactions for multi-write invariants, balance transfers, counters, ownership moves, and read-modify-write flows.
- Check isolation and concurrency behavior for races, unique conflicts, retries, idempotency, and deadlock handling.
- Keep transaction work bounded; avoid long network calls or slow external APIs inside database transactions.
- Review raw SQL for parameterization, search path assumptions, lock strength, concurrent index creation, and Postgres-version compatibility.
- Confirm migrations are reversible or have an explicit rollback/forward-fix plan.
- Verify test and CI paths exercise migrations against a real or representative Postgres database when migration risk is meaningful.
- Consider observability for migration duration, lock waits, failed backfills, and production query regressions.

## Common Review Smells

- Migration drops or renames a column in the same deploy that application code still reads.
- `NOT NULL` or unique constraint added without backfill and duplicate/null analysis.
- Prisma schema changes without committed migration files or generated client update.
- Broad `include` trees returned to API clients.
- Offset pagination without stable ordering on large or user-facing lists.
- Multi-step mutations use separate queries without a transaction or idempotency key.
- Raw SQL interpolates strings or assumes a local-only Postgres extension.
- Rollback plan is "restore from backup" for a routine deploy.

## Primary-Source Refresh Triggers

- Prisma, Prisma Migrate, Prisma Client, or Postgres version upgrades.
- Migration SQL uses newer Postgres features, locks, concurrent indexes, generated columns, enums, extensions, partitions, or isolation-level behavior.
- Data-loss, backfill, tenant isolation, payment/accounting, or compliance-sensitive schema changes.
- Query performance depends on planner behavior, relation loading, pagination strategy, or raw SQL.
- Deploy process changes the order of app release, migration execution, generated client deployment, or rollback.
