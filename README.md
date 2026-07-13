# php-obs-dev-base

PHP observability dev/CI metapackage. The concrete counterpart to [`php-obs-base`](https://github.com/just-bases/php-obs-base).

## Why this exists, and why it's separate

`php-obs-base` is deliberately flat — API-only, contracts-only (OTel API, CloudEvents, PSR-3). That's correct for anything a *library* depends on: consumers shouldn't have an exporter, an SDK, or a concrete logger forced on them by a package they didn't choose.

But tests and CI need to actually *run* observability code — assert on real span output, real log lines, real event serialization — not mock an interface in every package's test suite individually. That's a dev/CI concern, not a runtime-consumer concern, so it gets its own metapackage rather than being bundled into `php-obs-base` itself.

**Rule of thumb:** `php-obs-base` goes in `require`. `php-obs-dev-base` goes in `require-dev`, and only in the package actually running the tests — not in every downstream consumer.

## What it requires

```json
"just-bases/php-obs-base": "^1.0",
"open-telemetry/sdk": "^1.9",
"open-telemetry/exporter-otlp": "^1.1",
"open-telemetry/transport-grpc": "^1.1",
"monolog/monolog": "^3.7"
```

- **php-obs-base** — pulls in the flat contracts (OTel API, CloudEvents, PSR-3) this package builds on.
- **OTel SDK** — the concrete implementation. Lets tests exercise real span creation/export instead of a no-op API implementation.
- **OTel OTLP exporter + gRPC transport** — export path for local dev (point at a local collector/Jaeger) and CI (assert against captured span data).
- **Monolog** — concrete PSR-3 implementation for dev/CI logging assertions.

## Usage

```bash
composer require --dev just-bases/php-obs-dev-base
```

## Status

New. First real dev/CI counterpart to a `-base` package — if this pattern holds up, `php-unit-test-base` and `php-qa-base` may eventually get non-flat siblings too, but only if a real need for one surfaces the same way this one did.
