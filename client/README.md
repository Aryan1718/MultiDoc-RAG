# MultiDoc-RAG Client

`client/` is the React frontend for MultiDoc-RAG. It handles Supabase auth, workspace onboarding, multi-file upload flows, document monitoring, single-document chat, and observability dashboards.

## What It Does Today

- signs users in and out with Supabase
- gates the app on workspace creation
- prepares and completes batch uploads through signed URLs
- shows ingestion-run progress, queue-aware document states, and retry actions
- displays usage and observability metrics
- streams grounded chat responses for the current active document

## Recent Frontend Updates

- `08f452d` on 2026-06-08: upload flow now supports up to 50 files per run, accepted/rejected file summaries, grouped processing states, and retry-oriented ingestion UX
- `0409162` on 2026-06-08: observability and upload views now surface ingestion timing
- `b0d4fd2` on 2026-06-08: frontend dependency vulnerabilities were addressed

## Important Current Constraint

The upload UX is multi-document aware, but the chat UX is still single-document oriented. The backend query API already accepts `document_ids`, but [client/src/pages/ChatPage.tsx](/D:/Desktop/projects/MultiDoc-RAG/client/src/pages/ChatPage.tsx:1) still submits one active `document_id`.

## Main Routes

Public:
- `/login`
- `/signup`

Authenticated:
- `/workspace`
- `/app/upload`
- `/app/chat`
- `/app/observability`
- `/app/workspace`

## Key Files

- [src/context/AuthContext.tsx](/D:/Desktop/projects/MultiDoc-RAG/client/src/context/AuthContext.tsx:1)
- [src/lib/api.ts](/D:/Desktop/projects/MultiDoc-RAG/client/src/lib/api.ts:1)
- [src/pages/UploadPage.tsx](/D:/Desktop/projects/MultiDoc-RAG/client/src/pages/UploadPage.tsx:1)
- [src/components/upload/UploadPanel.tsx](/D:/Desktop/projects/MultiDoc-RAG/client/src/components/upload/UploadPanel.tsx:1)
- [src/pages/ChatPage.tsx](/D:/Desktop/projects/MultiDoc-RAG/client/src/pages/ChatPage.tsx:1)
- [src/pages/ObservabilityPage.tsx](/D:/Desktop/projects/MultiDoc-RAG/client/src/pages/ObservabilityPage.tsx:1)

## APIs Used

- `GET /auth/me`
- `POST /workspaces`
- `GET /workspaces/me`
- `GET /documents`
- `GET /documents/{document_id}`
- `GET /documents/{document_id}/pages/{page_number}`
- `GET /documents/ingestion-runs/{run_id}`
- `GET /documents/ingestion-queues`
- `POST /documents/upload-prepare`
- `POST /documents/upload-complete`
- `POST /documents/upload-prepare-batch`
- `POST /documents/upload-complete-batch`
- `POST /documents/{document_id}/retry`
- `POST /documents/{document_id}/reindex`
- `DELETE /documents/{document_id}`
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

## Environment

```bash
VITE_API_URL=http://localhost:8000
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key
```

## Run

```bash
cd client
npm install
npm run dev -- --host 0.0.0.0
```
