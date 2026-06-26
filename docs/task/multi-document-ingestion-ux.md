# Task: Multi-Document Ingestion UX

## Status

Partially implemented. The upload workflow improved substantially in `08f452d`, but this task is not fully complete.

## What Has Already Been Done

The current upload UX already includes:
- multi-file selection
- pre-upload accepted and rejected file handling
- batch prepare and complete integration
- ingestion-run awareness
- live processing summaries
- status grouping for active, ready, and failed documents
- retry affordances for failed documents
- search, sort, and filter support for larger document sets

Primary files:
- `client/src/pages/UploadPage.tsx`
- `client/src/components/upload/UploadPanel.tsx`
- `client/src/lib/api.ts`

## What Is Still Missing

- a true multi-document query/chat selection flow
- clearer run-history or batch-history UX beyond the current active upload experience
- stronger operator-facing ingestion health presentation in the frontend
- any additional UX adjustments discovered after larger real load tests

## Goal

Continue evolving the frontend so multi-document ingestion feels like one coherent workflow from selection through recovery, while staying aligned with the backend ingestion-run model that now exists.
