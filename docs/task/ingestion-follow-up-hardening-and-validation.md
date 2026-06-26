# Task: Ingestion Follow-Up Hardening and Validation

## Status

This is now a follow-up task list, not a pure design draft. Several items from the original list have already landed.

## Already Implemented

- repeatable ingestion load-test tooling
- ingestion timing in the frontend dashboard
- stale document and ingestion-run reconciliation
- backend support for multi-document query contracts
- broader ingestion API and retrieval test coverage

## Still Worth Doing

1. run larger repeatable benchmarks and keep the results comparable over time
2. add deeper operator observability such as queue-depth history and worker-level timing/latency logging
3. finish any remaining schema/runtime status cleanup
4. extend the frontend chat/query UX to take advantage of multi-document backend support
5. keep expanding automated tests around edge-case failure and recovery flows

## Why This Task Still Exists

The hardest remaining questions are no longer “can the pipeline work?” but:
- how well it behaves under sustained load
- how clearly operators can diagnose slowdowns or failures
- how much of the backend multi-document capability is exposed cleanly in the UI
