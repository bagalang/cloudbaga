# cloudbaga — plan

Date: 2026-08-05
Status: **P0 done** (C1–C4) · **0.2.0** trace on all routes + PORT clamp (2026-08-24)
Goal: K8s-demoable service surface.

## P0 ✅

1. C1 signal builtins
2. C2 metbaga
3. C3 logbaga
4. cloudbaga routes + graceful stop

## P1

- Multi-connection (go_bg per conn) while still draining on SIGTERM
- Histogram metrics + label sets
- Request-id middleware — **partial**: `traceparent` on every response;
  `trace_id` JSON on `/` is escaped.
