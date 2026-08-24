# cloudbaga — gaps

## G1 — serial connections

Accept handles one request per connection then closes (no keep-alive /
no go_bg). Fine for the probe; production should use httpdbaga threads
mode + a drain gate on SIGTERM.

## G2 — readiness is process-local only

No dependency checks (Postgres, etc.). `/readyz` is "not shutting down".

## G3 — hello JSON concatenated `trace_id` raw

**Symptom.** `,"trace_id":"` + hex + `"` was safe only because ids are
hex. A future field with quotes would break the object.

**Workaround.** `json_escape` for `msg` and `trace_id`.

**Verdict.** Closed 2026-08-24.

## G4 — `PORT=` garbage listened on 0

**Symptom.** `parse_int("abc")` is 0; `tcp_listen(0)` binds an ephemeral
port. Same for 99999.

**Workaround.** `cloud_port(raw, def)` keeps 1..65535, else default.

**Verdict.** Closed 2026-08-24.

## G5 — traceparent only on `/` and gRPC

**Symptom.** `/healthz`, `/metrics`, 404 did not echo `traceparent`.

**Workaround.** `cloud_with_trace` / `cloud_json` on every response.

**Verdict.** Closed 2026-08-24.
