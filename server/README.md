# MultiDoc-RAG Server

`server/` is the FastAPI backend for MultiDoc-RAG. It owns auth validation, workspace resolution, document orchestration, query execution, token budgets, chat/session APIs, and observability.

## What Is Implemented

- Supabase JWT validation and workspace-scoped access
- single-file and batch upload prepare/complete APIs
- ingestion-run tracking, queue inspection, ingestion health, and reconciliation endpoints
- retry and reindex flows
- grounded query and streaming query APIs
- backend support for querying across multiple selected documents
- daily token reservation, commit, release, and usage reporting
- query history, citation lookup, and chat session APIs

## Recent Backend Updates

- `08f452d` on 2026-06-08: batch upload contracts, ingestion-run APIs, upload policy helpers, and stronger worker failure callbacks
- `9fd2a85` on 2026-06-08: stale ingestion reconciliation, multi-document query contract support, retrieval updates, and broader ingestion tests
- `f3ad7db` on 2026-06-06: token budget lock-contention fix for streaming queries and Supabase setup cleanup

## API Surface

- `GET /health`
- `GET /auth/me`
- `POST /workspaces`
- `GET /workspaces/me`
- `GET /documents`
- `GET /documents/{document_id}`
- `GET /documents/{document_id}/pages/{page_number}`
- `GET /documents/ingestion-queues`
- `GET /documents/ingestion-health`
- `POST /documents/ingestion-reconcile`
- `GET /documents/ingestion-runs/{run_id}`
- `POST /documents/upload-prepare`
- `POST /documents/upload-complete`
- `POST /documents/upload-prepare-batch`
- `POST /documents/upload-complete-batch`
- `POST /documents/{document_id}/retry`
- `POST /documents/{document_id}/reindex`
- `DELETE /documents/{document_id}`
- `POST /query`
- `POST /query/stream`
- `GET /citations/{chunk_id}`
- `GET /queries`
- `GET /queries/{query_id}`
- `POST /chats/sessions`
- `PATCH /chats/sessions/{session_id}`
- `GET /chats/sessions`
- `GET /chats/sessions/{session_id}`
- `GET /usage/today`
- `GET /usage/observability`

## Query Model Today

The server accepts both legacy `document_id` and new `document_ids` payloads. Query validation and retrieval can span up to `10` selected documents and up to `100` total pages.

Relevant files:
- [app/schemas/query.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/schemas/query.py:1)
- [app/api/query.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/api/query.py:1)
- [app/api/query_stream.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/api/query_stream.py:1)
- [app/core/retrieval.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/core/retrieval.py:1)

## Ingestion Model Today

- file validation begins in the API
- upload completion verifies the storage object and enqueues extract jobs
- extraction revalidates file shape and page/content limits
- indexing batches embeddings and writes chunks/vectors idempotently
- reconciliation can fail stale active documents and refresh ingestion runs

Relevant files:
- [app/api/documents.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/api/documents.py:1)
- [app/core/ingestion_policy.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/core/ingestion_policy.py:1)
- [app/core/ingestion_reconciliation.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/core/ingestion_reconciliation.py:1)
- [app/core/ingestion_runs.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/core/ingestion_runs.py:1)

## Environment Highlights

Important settings in [app/config.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/config.py:1):
- `MAX_FILE_SIZE_BYTES=10485760`
- `MAX_PDF_PAGE_COUNT=10`
- `MAX_BULK_UPLOAD_FILES=50`
- `MAX_QUERY_DOCUMENTS=10`
- `MAX_QUERY_TOTAL_PAGES=100`
- `EMBEDDING_BATCH_SIZE=32`
- `INGEST_EXTRACT_JOB_TIMEOUT_SECONDS=900`
- `INGEST_INDEX_JOB_TIMEOUT_SECONDS=1800`

## Run

```bash
cd server
pip install -r requirements.txt
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```
