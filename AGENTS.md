# AGENTS.md
# MultiDoc-RAG System (v1) - Production-Ready Architecture

## Context First

Before planning changes or implementing tasks, read `docs/project-context.md` to understand the current codebase, what is already implemented, and the known gaps between the architecture goal and the as-built system.

## Task Routing And Order

When a user asks what should be implemented next, what a task means, or asks to work on a specific improvement area, use the task docs in `docs/task/` as the source of truth for scope, expected behavior, and implementation direction.

Map requests to task files like this:

- Backend ingestion scalability, throughput, worker behavior, queue behavior, orchestration, or ingestion architecture:
  - `docs/task/ingestion-pipeline-scale-and-reliability.md`
- Reviewing or testing the ingestion workflow after implementation, finding gaps, bottlenecks, or follow-up improvements:
  - `docs/task/report-ingestion-workflow.md`
- Frontend or workflow improvements for uploading and managing many documents:
  - `docs/task/multi-document-ingestion-ux.md`
- File-size limits, page-count limits, validation rules, ingestion rejection behavior, retry rules, and failure handling:
  - `docs/task/document-ingestion-limits-and-failure-handling.md`

Recommended order of execution:

1. Read `docs/project-context.md`
2. Implement `docs/task/document-ingestion-limits-and-failure-handling.md`
3. Implement `docs/task/ingestion-pipeline-scale-and-reliability.md`
4. Run the evaluation/report work from `docs/task/report-ingestion-workflow.md`
5. Implement `docs/task/multi-document-ingestion-ux.md`

Ordering rules:

- Do not start UX-first if backend ingestion behavior, limits, and failure semantics are still changing.
- Treat document limits and failure handling as foundational because they affect API contracts, worker behavior, retry rules, and user messaging.
- Treat the ingestion scale/reliability task as the main backend improvement track.
- Use the report task after backend ingestion work to measure what is still weak before starting major UX refinement.
- UX work should reflect the actual backend model rather than inventing a frontend-only approximation unless clearly marked as temporary.

If the user asks to implement one task directly, still read the dependent earlier task docs when needed so the work stays aligned with the intended sequence.

## Naming Note

This repository and its documentation should use the product name `MultiDoc-RAG`.

## Repository Layout

```text
MultiDoc-RAG/
```

## Package Naming Example

```json
{
  "name": "multidoc-rag-client"
}
```
