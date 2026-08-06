# Observability Review Reference

Source snapshot: 2026-08-06.

Primary sources:
- OpenTelemetry documentation: https://opentelemetry.io/docs/
- OWASP Logging Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/Logging_Cheat_Sheet.html
- Google SRE monitoring distributed systems: https://sre.google/sre-book/monitoring-distributed-systems/

Use this reference when reviewing logs, metrics, traces, error reporting, alertability, background tasks, privacy-safe diagnostics, correlation IDs, or production debugging visibility.

## Review Checklist

- Identify the new failure modes introduced by the change and whether production operators can detect, diagnose, and prioritize them.
- Log important state transitions, dependency failures, background job failures, retries, dead-letter paths, and security-relevant denials with useful context.
- Keep logs structured enough to query: event names, status, resource IDs, tenant/account context when safe, correlation/request IDs, and error classes.
- Redact secrets, tokens, credentials, cookies, authorization headers, raw PII, payment data, and sensitive request/response bodies.
- Ensure errors carry enough context for debugging without leaking internals to clients.
- Add or preserve correlation IDs across HTTP requests, queues, jobs, traces, and downstream calls.
- Review metrics for volume, latency, error rate, saturation, queue depth, retry count, dropped jobs, migration/backfill progress, and business-critical outcomes.
- Check traces or spans for cross-service or async workflows where logs alone will be hard to reconstruct.
- Confirm alerts are actionable, tied to user impact or reliability symptoms, and avoid noisy per-error paging.
- Ensure background workers, scheduled jobs, webhooks, and async tasks report final failure, not just initial enqueue.
- Include observability in tests or validation when instrumentation, error reporting, or alert routing changes.

## Common Review Smells

- Catch block logs `"failed"` without error class, resource context, or correlation ID.
- Fire-and-forget job failures disappear after enqueue succeeds.
- Logs include full request bodies, tokens, cookies, OAuth payloads, or PII.
- New retries have no metric for exhaustion or retry storm behavior.
- Alert fires on every exception instead of user-impacting rate or symptom.
- Production debugging depends on reproducing locally with unavailable data.
- Error reports group unrelated failures because context/fingerprints are missing.

## Primary-Source Refresh Triggers

- Observability SDK, OpenTelemetry, error reporting, logging provider, tracing exporter, or metrics backend upgrades.
- New background jobs, queues, webhooks, scheduled tasks, multi-service calls, migrations, or async workflows.
- Security/privacy-sensitive diagnostics, audit logging, compliance logging, or PII handling changes.
- Incident fixes, alert routing changes, SLO/SLA changes, or production-debugging gaps.
- Runtime or deployment changes that affect stdout/stderr, log sampling, trace propagation, or environment metadata.
