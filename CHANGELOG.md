# Changelog

## Unreleased

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
