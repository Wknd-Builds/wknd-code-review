# CI And Deployment Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- GitHub Actions documentation: https://docs.github.com/actions
- Docker build best practices: https://docs.docker.com/build/building/best-practices/
- Twelve-Factor App config: https://12factor.net/config
- npm ci documentation: https://docs.npmjs.com/cli/v11/commands/npm-ci/

Use this reference when reviewing CI jobs, deployment configuration, Dockerfiles, caches, environment variables, secrets, build artifacts, runtime versions, migrations, release order, rollback, or production safety.

## Review Checklist

- Confirm CI runs the jobs that prove the changed behavior: lint, typecheck, unit, integration, E2E, migration, mobile, build, or package-specific jobs as appropriate.
- Check path filters, matrix entries, workspace filters, skipped packages, and allowed failures for accidental coverage gaps.
- Review cache keys for lockfile/runtime inputs and stale dependency risk.
- Use lockfile-driven installs in CI and production images; ensure package manager and Node/runtime versions match project policy.
- Review Dockerfiles for pinned base images, multi-stage builds, `.dockerignore`, non-root runtime users, minimal build context, health checks where useful, and no secret leakage into layers.
- Treat environment variables as deployment contract: required vars should be documented/validated, secrets should not appear in source, logs, build args, or client bundles.
- Check build artifacts for reproducibility, correct asset paths, source maps policy, server/client separation, and runtime compatibility.
- Review migration deploy order, generated clients, expand/backfill/contract sequencing, and rollback implications.
- Verify release and rollback can handle mixed-version app/database states.
- Ensure preview/staging behavior does not weaken security assumptions that users can reach.
- Confirm background workers, queues, cron jobs, and web services deploy in compatible order.
- Check production safety for feature flags, rate limits, maintenance windows, data backfills, and observability around new failure modes.

## Common Review Smells

- CI green because the changed package, workspace, app, or migration path was filtered out.
- Cache key ignores lockfile, runtime version, OS, or package-manager version.
- Docker build uses `npm install`, copies the whole repo before dependency install, runs as root, or leaves secrets in layers.
- Migration runs after new app code that already assumes the new schema.
- Rollback plan ignores database changes or background workers.
- Required env vars are read at runtime without startup validation.
- Build artifacts depend on local files not present in CI or production.

## Primary-Source Refresh Triggers

- CI provider, Docker, package manager, runtime, deployment platform, or base image upgrades.
- Changes to migration ordering, release automation, rollback process, artifact packaging, or environment variable contract.
- New secrets, build-time configuration, source maps, preview deployments, or public asset behavior.
- Changes that introduce workers, queues, scheduled jobs, mobile builds, or multi-service deploy dependencies.
- Security advisories or incidents involving build tooling, CI tokens, artifacts, or container images.
