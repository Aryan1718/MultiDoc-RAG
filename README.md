# MultiDoc-RAG

MultiDoc-RAG is a workspace-scoped PDF RAG platform for uploading many documents, processing them asynchronously, and querying indexed content with grounded citations.

The repository is a monorepo with three runtime surfaces:
- `client`: React + Vite frontend
- `server`: FastAPI API and orchestration layer
- `worker`: Redis/RQ ingestion workers

## Current State

The codebase has moved beyond the original single-file ingestion baseline. The current implementation includes:
- batch upload preparation and completion
- ingestion runs with grouped status tracking
- extraction and indexing on separate Redis/RQ queues
- document retry and reindex flows
- ingestion queue, health, and reconciliation endpoints
- multi-document query support in the backend API
- ingestion timing visibility in the frontend observability view
- workspace-scoped token budget tracking with reservation and cleanup

The main remaining product gap is the chat UX: the backend can query across multiple selected documents, but the current chat page still centers on one active document at a time.

## Recent Updates

Recent merged work that should now be reflected in the docs:

- `08f452d` on 2026-06-08: improved multi-document ingestion workflow with batch prepare/complete, ingestion runs, stronger upload UX, and worker callback coverage
- `9fd2a85` on 2026-06-08: hardened ingestion workflow, added reconciliation, load-test tooling, and multi-document query readiness in the API/retrieval layers
- `0409162` on 2026-06-08: added ingestion timing visibility to upload and observability dashboards
- `bffd3ed`, `b0d4fd2`, `d7ca2f1` on 2026-06-08: tightened security around load-test artifacts, frontend dependencies, and Trivy CI maintenance

## Architecture

```text
React client
  -> Supabase Auth for session tokens
  -> FastAPI server for workspace, document, query, usage, and chat APIs
  -> Supabase Storage for direct PDF uploads

FastAPI server
  -> Postgres/pgvector for metadata, pages, chunks, embeddings, usage, and logs
  -> Redis/RQ for ingestion queues and rate limiting
  -> OpenAI for embeddings and grounded answer generation

Workers
  -> ingest_extract: download PDF, validate, extract page text
  -> ingest_index: chunk pages, batch embeddings, persist vectors
  -> maintenance/reconciliation helpers for stale reservations and stuck ingestion state
```

## End-to-End Flow

1. The user signs in with Supabase Auth.
2. The server resolves the user workspace from the bearer token.
3. The client prepares one file or a batch with `/documents/upload-prepare` or `/documents/upload-prepare-batch`.
4. PDFs are uploaded directly to Supabase Storage using signed URLs.
5. The client confirms upload completion with `/documents/upload-complete` or `/documents/upload-complete-batch`.
6. The API enqueues extraction jobs on `ingest_extract`.
7. Extraction writes `document_pages`, applies page/content validation, and enqueues indexing.
8. Indexing builds page-bounded chunks, batches embeddings, writes vectors, and marks documents `ready` or `indexed`.
9. The client queries via `/query` or `/query/stream`.
10. The server embeds the question, retrieves top chunks across one or more selected documents, reserves tokens, calls the LLM, and records usage/query logs.

## Important Limits In Code Today

- max file size: `10 MB`
- max PDF pages: `10`
- max documents per workspace: `100`
- max bulk upload files per run: `50`
- max query question length: `500` chars
- max selected documents per query: `10`
- max total pages across selected query documents: `100`
- embedding batch size: `32`

These values come from the current code in [server/app/config.py](/D:/Desktop/projects/MultiDoc-RAG/server/app/config.py:1) and [.env.example](/D:/Desktop/projects/MultiDoc-RAG/.env.example:1).

## Repository Layout

```text
MultiDoc-RAG/
|-- client/
|-- server/
|-- worker/
|-- docs/
|-- scripts/
|-- artifacts/
|-- docker-compose.yml
|-- AGENTS.md
`-- README.md
```

## Primary Docs

- [docs/project-context.md](/D:/Desktop/projects/MultiDoc-RAG/docs/project-context.md:1): current implemented architecture and remaining gaps
- [docs/ingestion-workflow-evaluation.md](/D:/Desktop/projects/MultiDoc-RAG/docs/ingestion-workflow-evaluation.md:1): ingestion evaluation summary and caveats
- [docs/ingestion-load-testing.md](/D:/Desktop/projects/MultiDoc-RAG/docs/ingestion-load-testing.md:1): repeatable ingestion benchmark usage
- [docs/task/document-ingestion-limits-and-failure-handling.md](/D:/Desktop/projects/MultiDoc-RAG/docs/task/document-ingestion-limits-and-failure-handling.md:1)
- [docs/task/ingestion-pipeline-scale-and-reliability.md](/D:/Desktop/projects/MultiDoc-RAG/docs/task/ingestion-pipeline-scale-and-reliability.md:1)
- [docs/task/report-ingestion-workflow.md](/D:/Desktop/projects/MultiDoc-RAG/docs/task/report-ingestion-workflow.md:1)
- [docs/task/multi-document-ingestion-ux.md](/D:/Desktop/projects/MultiDoc-RAG/docs/task/multi-document-ingestion-ux.md:1)
- [docs/task/ingestion-follow-up-hardening-and-validation.md](/D:/Desktop/projects/MultiDoc-RAG/docs/task/ingestion-follow-up-hardening-and-validation.md:1)

## Local Development

```bash
docker-compose up --build
```

Useful services:
- client: `http://localhost:5173`
- server: `http://localhost:8000`
- RQ dashboard: `http://localhost:9181`

## Module Readmes

- [client/README.md](/D:/Desktop/projects/MultiDoc-RAG/client/README.md:1)
- [server/README.md](/D:/Desktop/projects/MultiDoc-RAG/server/README.md:1)
- [worker/README.md](/D:/Desktop/projects/MultiDoc-RAG/worker/README.md:1)
