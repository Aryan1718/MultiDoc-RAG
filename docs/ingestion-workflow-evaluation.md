# Ingestion Workflow Evaluation

Date: 2026-06-05

## Context

This evaluation captured the ingestion workflow before the 2026-06-08 hardening pass landed. Read it together with these later updates:
- `08f452d`: multi-document ingestion workflow improvements
- `9fd2a85`: reconciliation, load testing, and multi-document query readiness
- `0409162`: ingestion timing in dashboards

## Summary

The evaluation showed that the ingestion pipeline already worked for small local batches and correctly handled valid files, page-limit failures, and scanned/image-only failures. The main weaknesses at that point were stale run state, noisy failure reporting, and limited timing visibility.

## What Was Validated

- valid PDFs could move to `ready`
- page-limit failures were rejected
- scanned/image-only PDFs were rejected
- extraction and indexing ran on separate queues
- retry/recovery worked for previously stuck indexing cases

## What Changed After This Evaluation

Since this report was written, the codebase added:
- ingestion reconciliation for stale active documents and runs
- richer batch-upload and ingestion-run behavior
- multi-document query contract support in the backend
- ingestion timing surfaced in frontend dashboards
- repeatable load-testing tooling under `scripts/ingestion_load_test.py`

## Remaining Use

This document is still useful as a baseline audit, especially for understanding the problems that the June 8 changes were meant to solve. It should not be read as the latest state of the ingestion system by itself.
