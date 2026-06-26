# Task: Document Ingestion Limits and Failure Handling

## Status

Mostly implemented, with some follow-up cleanup still useful.

## What Has Already Been Done

The current code now enforces and communicates most of the important ingestion rules:
- PDF-only uploads
- max file size checks in API and worker paths
- max page-count enforcement during extraction
- unsupported scanned/image-only PDF rejection
- structured failure-message helpers
- retryability classification
- retry and reindex flows
- stale-state reconciliation for stuck ingestion work

Primary files:
- `server/app/config.py`
- `server/app/api/documents.py`
- `server/app/core/ingestion_policy.py`
- `worker/jobs/ingest_extract.py`
- `worker/jobs/ingest_index.py`

## Current Limits

- maximum file size: `10 MB`
- maximum page count: `10`
- supported content type: `application/pdf`
- supported content mode: text-based PDFs only

## Remaining Work

- keep docs and schema references aligned with the actual runtime status model
- keep error wording consistent across API responses, worker failures, and frontend messaging
- validate that operator views clearly separate expected rejections from infrastructure failures
- re-run larger ingestion tests to confirm failure handling remains predictable under load

## Expected Behavior

- reject invalid files as early as possible
- revalidate critical limits during ingestion
- avoid starting downstream work for known-invalid inputs
- mark terminal failures clearly and safely
- allow retry only where the failure is meaningfully retryable
