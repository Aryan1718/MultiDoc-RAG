# Project Context

## Purpose

MultiDoc-RAG is a workspace-scoped PDF RAG system with three main surfaces:
- `client`: React + Vite frontend
- `server`: FastAPI API
- `worker`: Redis/RQ ingestion workers

The system lets a user sign in with Supabase, upload one or many PDFs, process them asynchronously, and query indexed content with grounded citations.

## What Is Implemented

### 1. Authentication and Workspace Scoping

- Supabase Auth is the user identity source.
- The API validates bearer tokens and resolves the current workspace server-side.
- Data access is consistently scoped by `workspace_id`.

Primary files:
- `client/src/context/AuthContext.tsx`
- `server/app/api/deps.py`
- `server/app/core/auth.py`
- `server/app/api/workspaces.py`

### 2. Multi-Document Upload and Ingestion Runs

- The API supports both single-file and batch upload flows.
- `upload-prepare-batch` creates an `ingestion_run_id` and returns per-file prepare results.
- `upload-complete-batch` confirms uploaded objects and enqueues extraction jobs.
- The frontend upload flow now treats a multi-file upload as one run with accepted/rejected/processing/ready/failed summaries.

Primary files:
- `client/src/components/upload/UploadPanel.tsx`
- `client/src/pages/UploadPage.tsx`
- `server/app/api/documents.py`
- `server/app/schemas/documents.py`

### 3. Background Ingestion

- `ingest_extract` downloads PDFs from Supabase Storage, validates file/page/content rules, writes `document_pages`, and enqueues indexing.
- `ingest_index` chunks page text, batches embeddings, writes chunks/vectors, commits token usage, and marks documents queryable.
- Worker failure callbacks and ingestion reconciliation reduce the chance of documents staying stuck forever.

Primary files:
- `worker/jobs/ingest_extract.py`
- `worker/jobs/ingest_index.py`
- `worker/jobs/ingest_callbacks.py`
- `server/app/core/ingestion_reconciliation.py`

### 4. Retrieval and Querying

- The API supports standard query and streaming query flows.
- Query payloads can use legacy `document_id`, new `document_ids`, or both.
- Retrieval can search across a selected set of ready documents.
- Query logs store the searched document set.

Important current limitation:
- the backend is multi-document capable, but the main chat UX still centers on one active document

Primary files:
- `server/app/schemas/query.py`
- `server/app/api/query.py`
- `server/app/api/query_stream.py`
- `server/app/core/retrieval.py`
- `client/src/pages/ChatPage.tsx`

### 5. Token Budget Control

- Query-time LLM usage is reserved before execution and committed after actual usage is known.
- Indexing reserves and commits embedding usage during batch embedding work.
- Stale reservation cleanup exists for stranded reservations.

Primary files:
- `server/app/core/token_budget.py`
- `worker/jobs/ingest_index.py`
- `worker/jobs/maintenance.py`

### 6. Observability and Ingestion Health

- Usage and query observability endpoints are implemented.
- The documents API now exposes queue counts, ingestion health, run status, and reconciliation endpoints.
- The frontend observability page shows query metrics plus average extraction/index/total ingestion timing based on document timing fields.

Primary files:
- `server/app/api/usage.py`
- `server/app/api/documents.py`
- `client/src/pages/ObservabilityPage.tsx`

### 7. Load-Testing Support

- The repository includes `scripts/ingestion_load_test.py`.
- Load-test artifacts are stored under `artifacts/ingestion-load/`.

Primary files:
- `scripts/ingestion_load_test.py`
- `docs/ingestion-load-testing.md`

## Recent Shipped Changes

Recent merged work reflected in the codebase:
- `08f452d` on 2026-06-08: multi-document ingestion workflow improvements
- `9fd2a85` on 2026-06-08: ingestion hardening and multi-document query readiness
- `0409162` on 2026-06-08: ingestion timing in dashboards
- `f3ad7db` on 2026-06-06: token budget lock-contention fix for streaming queries

## Important Limits In Code Today

- max file size: `10 MB`
- max PDF pages: `10`
- max documents per workspace: `100`
- max bulk upload files: `50`
- max question length: `500`
- max selected documents per query: `10`
- max total pages across query documents: `100`

Source of truth:
- `server/app/config.py`
- `.env.example`

## Current Gaps

The main gaps that still matter:
- chat UX is still single-document even though backend query supports multiple documents
- observability is stronger than before but still lacks persistent queue-depth history and deeper worker-level metrics
- docs and schema-history still contain traces of older status models and pre-batch assumptions
- the repository should still be treated as a strong working baseline, not a fully finished production system

## Purpose of `docs/task`

The task docs should be read as implementation guidance and follow-up planning, not as a description of the original untouched baseline. Several backend ingestion tasks are now partially or mostly implemented and should be read with that updated context in mind.
