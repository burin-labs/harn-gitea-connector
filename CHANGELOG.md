# Changelog

## Unreleased

- Security sweep 2026-05-23 (hardening, fail-closed defaults):
  - **F1 (CRITICAL) SSRF:** `__api_url` no longer treats absolute-URL `path` arguments as an
    escape from the configured `api_base_url`; absolute URLs are rejected so the attached
    `Authorization: Bearer` cannot be redirected to attacker-chosen hosts.
  - **F3 (HIGH) fail-closed:** webhook delivery is now rejected with 401 `missing_signature`
    when no signing secret is configured for the binding (previously the request was accepted
    as `unsigned`).
  - **F5 (HIGH) trusted secret resolution:** `__signing_secret` no longer reads
    `raw.signing_secret`/`raw.secret`/`raw.webhook_secret`/`raw.config.signing_secret`/
    `raw.secrets.signing_secret`. Secrets are resolved from orchestrator `ctx`, the per-binding
    state seeded via `activate()`, or `$GITEA_SIGNING_SECRET` only.
  - **F6 (HIGH) body source:** signature verification runs against `raw.body_text` when
    present, else over the decoded bytes of `raw.body_base64`. When neither is present the
    request is rejected with 400 `missing_body` instead of silently HMAC'ing over `""`.
  - **F7 (MEDIUM) exact event match:** `__supports_event` matches exact names or fully-qualified
    subkinds (`push.created`), no longer a bare prefix that would let `pushy` spoof `push`.
  - **F8 (MEDIUM) require binding_id:** inbound requests without a `binding_id` are rejected
    with 400 `missing_binding_id`; the implicit single-binding fallback is removed.
  - **F9 (MEDIUM) dedupe key:** derived from the host-set `x-gitea-delivery` header only.
    When absent, falls back to a hash of `body_base64`, `received_at`, and `binding_id` —
    never attacker-controlled JSON fields.
  - **F13 (LOW) shutdown gating:** documented `@host_only` semantics for `shutdown()`. Full
    caller-identity gating remains a host responsibility.
  - **F14 (LOW) verify before parse:** signature verification now runs ahead of `json_parse`,
    so unsigned/oversized payloads cannot exhaust the parser before the signature check.
  - Deferred (tracked as future TODOs): F12 (switch outbound dispatch to
    `connector_http_request` helper), F15 (bump-harn workflow scoping).
- Adopt `std/connectors/shared` helpers: `verify_hmac_signature` for webhook HMAC checks,
  `rate_limit_token_bucket` for client-side throttling, and `paginate_cursor` for paginated
  list traversal.
- Reactive rate-limit handling on outbound API requests: honor `x-ratelimit-remaining` /
  `x-ratelimit-reset` and retry once on 429/503 when the reset is within 60 seconds.
- New outbound methods: `pull_requests.list`, `pull_requests.list_all`, `issues.list`,
  `issues.list_all`, `repositories.list`, and `rate_limit.acquire`.
- Declare `capabilities = ["webhook", "rate_limit", "pagination"]` so the connector parity
  matrix matches the github/linear/slack baseline.

## v0.1.0

- Initial pre-alpha Gitea connector package implementing Harn Connector contract v1.
