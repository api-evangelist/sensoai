---
name: Senso — ingest sources and search your knowledge base
description: Add raw content to a Senso knowledge base, then run semantic search that returns an AI-generated answer plus the source chunks and content IDs.
api: Senso Org API (https://apiv2.senso.ai/api/v1)
operations:
  - add_raw_content   # POST /content/raw
  - search_content    # POST /search
auth: X-API-Key header (key prefixed tgr_)
---

# Ingest and search a Senso knowledge base

Use this to load ground-truth content into Senso and query it so an agent answers from verified sources.

## Auth
Send every request with the header `X-API-Key: <your tgr_ key>` and `Content-Type: application/json`.
Base URL: `https://apiv2.senso.ai/api/v1`.

## Steps

1. **Ingest a source** — `POST /content/raw` with `{ "title", "summary", "text" }` (only `text` is required).
   The response returns the new content's `id` (prefixed `cnt_`). Repeat for each source, or use the CLI
   `senso ingest upload <files...>` for files.

2. **Wait for indexing** — ingestion is asynchronous; poll the content item (`GET /content/{id}`, CLI
   `senso content get <id>`) until it is processed before searching.

3. **Search** — `POST /search` with `{ "query", "max_results" }`. The response has `answer` (AI-generated)
   and `results[]`, each with `title`, `chunk_text`, and `content_id`. For chunks only (faster, no AI answer)
   use `senso search context "<query>"`.

## Rules
- Keep the query natural-language; results are semantic, not keyword.
- Cite `content_id` from results back to the user so answers are traceable to ground truth.
- Errors surface as non-2xx responses; no problem+json envelope is published, so treat any non-2xx as failure and surface the message.
