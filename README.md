# harn-gitea-connector

Pure-Harn Gitea connector: signed webhooks, self-hosted REST dispatch, and raw API helpers.

This package implements the Harn Connector interface contract v1 for `gitea`.
It normalizes inbound webhook payloads to the tagged `NormalizeResult` envelope,
verifies provider-specific webhook signatures, and exposes outbound raw API helpers
plus common PR/comment/status method aliases.

## Install

```sh
harn add github.com/burin-labs/harn-gitea-connector@v0.1.0
```

Until a version is tagged, depend on a path checkout:

```toml
[dependencies]
harn-gitea-connector = { path = "../harn-gitea-connector" }
```

## Webhook verification

The connector accepts unsigned events only when no verification material is
configured. Configure `signing_secret` for HMAC-based providers or `public_key`
for SourceHut Ed25519 verification.

## Authentication

Outbound calls use access token scoped to the target Gitea instance. Pass `access_token`, `token`,
`personal_access_token`, or `app_password` in the call args, or set the
`GITEA_TOKEN` environment variable.

## Development

```sh
harn check src/lib.harn
harn fmt --check src/lib.harn tests/*.harn
for test in tests/*.harn; do harn run "$test" || exit 1; done
```

## License

Dual-licensed under MIT and Apache-2.0.
