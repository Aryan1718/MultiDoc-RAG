# Task: Ingestion Workflow Evaluation

## Status

Evaluation baseline exists in `docs/ingestion-workflow-evaluation.md`, but it predates the 2026-06-08 hardening work. Future report work should measure the current codebase, not restate the old baseline.

## Purpose

This task is for testing and evaluating the ingestion workflow after implementation changes, then writing a grounded report about:
- what is working
- what is still weak
- where throughput breaks down
- whether ingestion is operationally understandable
- what should be improved next

## Current Baseline

The current codebase is no longer single-file only. It now includes:
- batch upload prepare and complete
- ingestion runs
- queue and ingestion health endpoints
- reconciliation for stale active ingestion state
- embedding batching
- backend support for multi-document query contracts

Relevant files:
- `docs/project-context.md`
- `server/app/api/documents.py`
- `worker/jobs/ingest_extract.py`
- `worker/jobs/ingest_index.py`
- `server/app/core/ingestion_reconciliation.py`

## Evaluation Questions

Focus the report on:
1. throughput under 10, 25, and 50 document runs
2. queue balance between extraction and indexing
3. correctness of ingestion-run terminal states
4. retry/reindex behavior after failures and reconciliation
5. token-budget behavior during larger ingestion runs
6. usefulness of timing and health data for operators
7. whether the remaining frontend and observability gaps block larger-scale use

## Deliverable

Produce a short report with:
- executive summary
- environment and worker setup
- scenario matrix
- performance findings
- reliability findings
- remaining gaps
- ranked next improvements
