# Task: Ingestion Pipeline Scale and Reliability

## Status

Partially implemented. The main backend ingestion expansion landed across `08f452d` and `9fd2a85`, but this area still has follow-up work.

## What Has Already Been Done

The codebase now includes:
- batch upload prepare and complete endpoints
- ingestion runs and grouped status tracking
- embedding batching
- richer failure classification helpers
- queue status and ingestion health APIs
- stale ingestion reconciliation
- worker callback coverage for failed/timed-out jobs

## Goal

Continue improving the ingestion pipeline so MultiDoc-RAG can ingest larger numbers of small PDFs with better throughput, safer failure handling, and clearer operational visibility.

## Remaining Focus Areas

1. higher-volume throughput validation under realistic load
2. deeper ingestion observability beyond current dashboard timing
3. clearer separation between expected validation failures and infrastructure failures in operator views
4. remaining status/schema cleanup and consistency work
5. any worker or DB bottlenecks discovered through real load testing

## Constraints In Code Today

- max file size: `10 MB`
- max pages per PDF: `10`
- max bulk upload files per run: `50`
- extraction timeout: `900s`
- indexing timeout: `1800s`
- embedding batch size: `32`

## Non-Goals

- redesigning the whole client
- OCR support
- changing core storage providers
- replacing pgvector

## Next Useful Work

- add durable queue-depth history or operator metrics
- benchmark 10/25/50 document runs and compare revisions
- tighten ingestion-run lifecycle semantics where edge cases remain
- improve cross-surface status vocabulary so frontend, backend, worker, and schema stay aligned
