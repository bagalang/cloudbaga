# cloudbaga

**12-factor cloud demo** — Track C flagship probe (C1–C4). **0.2.0**

This repository is the product. The compiler, `std`, `httpdbaga`,
`metbaga`, `logbaga`, `otelbaga`, and `pbbaga` stay in the baga
language monorepo. Check this tree out as `app-product/cloudbaga`
there (git submodule) so path deps and `-I app-product` keep working.

## Checkout

Inside a baga language clone:

```bash
git submodule update --init app-product/cloudbaga
# or, first time from a fresh baga tree without the submodule recorded:
git clone git@github.com:bagalang/cloudbaga.git app-product/cloudbaga
```

`sandak.toml` keeps path deps so the shared packages stay in baga.
`tests/cloud_test.baga` also stays in baga.

| Endpoint | Role |
|----------|------|
| `GET /healthz` | Liveness |
| `GET /readyz` | Readiness (503 after SIGTERM) |
| `GET /metrics` | Prometheus text (`metbaga`) |
| `GET /` | Hello JSON |

Graceful shutdown: `signal_watch(SIGTERM|SIGINT)` + `poll_wait` accept
loop so the process notices the signal between connections, flips ready
off, logs a JSON line (`logbaga`), and exits.

```bash
PORT=18080 ./baga -I . -I app-product app-product/cloudbaga/server.baga
# elsewhere:
curl -s localhost:18080/healthz
curl -s localhost:18080/metrics
kill -TERM <pid>
```

Env: `PORT` (default 18080; invalid/out-of-range values keep the default).

Every response carries `traceparent` when the incoming header parses (or
a new root is minted). `GET /` JSON `trace_id` is `json_escape`d.

## License

[MIT](LICENSE) — Copyright (c) 2026 Dim Gigov.
