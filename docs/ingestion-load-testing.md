# Ingestion Load Testing

Use `scripts/ingestion_load_test.py` to run repeatable ingestion benchmarks against the real MultiDoc-RAG API, signed upload flow, Redis queues, and workers.

## What This Covers

- batch upload prepare and complete
- polling ingestion runs to terminal state
- queue snapshots during processing
- per-run JSON artifacts for comparison across revisions

## Prerequisites

- Docker Compose services are running
- schema changes have been applied with `scripts/schema.local.sql` or `scripts/schema.supabase.sql`
- you have a valid bearer token for a user with a workspace

## PowerShell Examples

```powershell
cd D:\Desktop\projects\MultiDoc-RAG
$env:RAG_BEARER_TOKEN = "<paste access token>"
python scripts\ingestion_load_test.py --count 10 --scenario valid
python scripts\ingestion_load_test.py --count 25 --scenario valid
python scripts\ingestion_load_test.py --count 50 --scenario valid
python scripts\ingestion_load_test.py --count 10 --scenario mixed
```

## Output

Artifacts are written to:

```text
artifacts/ingestion-load/
```

Each artifact includes:
- prepare and complete responses
- ingestion-run state
- document snapshots
- queue snapshots collected while polling
- total wall-clock duration
- upload and prepare timing

## Recent Related Changes

- `9fd2a85` on 2026-06-08: added load-test tooling and supporting ingestion hardening
- `bffd3ed` on 2026-06-08: fixed security scan findings for generated load-test artifacts
