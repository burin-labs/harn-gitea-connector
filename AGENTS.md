# AGENTS.md

Pure-Harn connector package for self-hosted Gitea instances.

Shared connector authoring rules live in the Harn guide:

- [Connector authoring guide](https://github.com/burin-labs/harn/blob/main/docs/src/connectors/authoring.md)

Put shared connector guidance in the Harn guide and keep only
provider-specific notes and local hazards here.

`CLAUDE.md` points here. Edit `AGENTS.md` only.

## Provider notes

- Webhook event names use `x-gitea-event`; delivery ids use `x-gitea-delivery`.
- Webhook signatures use `x-gitea-signature` HMAC. Every request must identify
  its binding and carry that binding's signing secret.
  `verify_hmac_signature` from `std/connectors/shared` accepts both
  `sha256=<hex>` and bare hex bodies.
- Reactive rate-limit handling reads `x-ratelimit-remaining` and
  `x-ratelimit-reset` (Unix seconds) on every API response. On a 429 or 503
  with `remaining=0` and a reset within 60 seconds, the connector sleeps and
  retries once; resets further out return a `rate_limited` error.
- `rate_limit.acquire` exposes `rate_limit_token_bucket` from
  `std/connectors/shared` for proactive client-side throttling. State is keyed
  by `scope` and persisted in the connector store; tests inject `now_ms` for
  determinism.
- List endpoints (`pull_requests.list`, `issues.list`, `repositories.list`)
  use Gitea `?page=&limit=` pagination. Their `*.list_all` variants walk pages
  through `paginate_cursor`, stopping at a partial page or `max_pages`.
- The default API base URL is a placeholder for local tests. Real deployments
  should pass `api_base_url` and an access token or PAT, or set `GITEA_TOKEN`.

<!-- BEGIN HARN SHARED AGENT CONTRACT: managed by harn-bump-fleet -->

## Ecosystem working agreement

- Pursue the ambitious product outcome; make the seams boring with small typed
  interfaces, explicit invariants, and deterministic projections.
- Give each behavior one semantic owner. Generate or parity-test other surfaces
  instead of maintaining competing implementations.
- Work autonomously inside approved scope. Pause for destructive, production,
  high-spend, ambiguous, or authority-expanding actions—not routine reversible work.
- Treat stop, wait, stand down, and pivot as control events for long-lived work.
- Match evidence to the claim: exercise the canonical user path, state the
  falsifier, verify liveness and recovery, and record residual blind spots.
- "Ship" means landed on main with required deploy and post-merge checks complete.

<!-- END HARN SHARED AGENT CONTRACT -->
