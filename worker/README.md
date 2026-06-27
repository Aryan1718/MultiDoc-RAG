# MultiDoc-RAG Worker

`worker/` runs the asynchronous ingestion pipeline for MultiDoc-RAG. It handles PDF extraction, chunk indexing, embedding generation, and maintenance-style recovery tasks that do not belong on the request path.

## What It Does Today

- consumes `ingest_extract` and `ingest_index` RQ jobs
- downloads uploaded PDFs from Supabase Storage
- validates size, page-count, and extractable-text requirements during extraction
- writes `document_pages`
- chunks page text and batches embeddings
- writes `chunks` and `chunk_embeddings`
- marks documents ready or failed
- releases stale token reservations and supports ingestion recovery helpers

## Recent Worker Updates

- `08f452d` on 2026-06-08: stronger extraction/index status handling, idempotent run metadata, and explicit failure callbacks
- `9fd2a85` on 2026-06-08: refreshed ingestion-run state during worker progress, better failure-path handling, and tests for ingestion run recovery

## Queues

- `ingest_extract`
- `ingest_index`

The worker entrypoint in [worker.py](/D:/Desktop/projects/MultiDoc-RAG/worker/worker.py:1) can listen to one or both queues through `QUEUE_NAME`.

## Job Modules

- [jobs/ingest_extract.py](/D:/Desktop/projects/MultiDoc-RAG/worker/jobs/ingest_extract.py:1)
  - downloads PDFs
  - enforces `10 MB` and `10 page` limits during extraction
  - rejects scanned/image-only PDFs with no usable text
  - writes page rows and enqueues indexing

- [jobs/ingest_index.py](/D:/Desktop/projects/MultiDoc-RAG/worker/jobs/ingest_index.py:1)
  - reads extracted pages
  - creates page-bounded chunks
  - batches embeddings with `EMBEDDING_BATCH_SIZE`
  - persists vectors and token usage
  - rebuilds chunk/vector rows safely for retry and reindex flows

- [jobs/maintenance.py](/D:/Desktop/projects/MultiDoc-RAG/worker/jobs/maintenance.py:1)
  - clears stale token reservations

- [jobs/ingest_callbacks.py](/D:/Desktop/projects/MultiDoc-RAG/worker/jobs/ingest_callbacks.py:1)
  - marks active ingestion jobs failed when RQ kills or times out a job

## Run

```bash
cd worker
pip install -r requirements.txt
QUEUE_NAME=ingest_extract python worker.py
QUEUE_NAME=ingest_index python worker.py
```
