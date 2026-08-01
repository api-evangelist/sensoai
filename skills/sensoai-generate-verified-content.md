---
name: Senso — generate verified content from your knowledge base
description: Generate on-brand content grounded in a Senso knowledge base, using a content type and instructions, and optionally save it.
api: Senso Org API (https://apiv2.senso.ai/api/v1)
operations:
  - generate_content       # POST /generate
  - generate_with_prompt   # POST /generate/prompt
  - list_templates         # GET /templates
auth: X-API-Key header (key prefixed tgr_)
---

# Generate verified content with Senso

Use this to produce content that is grounded in verified knowledge-base sources rather than the model's priors.

## Auth
Header `X-API-Key: <your tgr_ key>`, `Content-Type: application/json`, base URL `https://apiv2.senso.ai/api/v1`.

## Steps

1. **(Optional) pick a content type/template** — `GET /templates` (CLI `senso content-types list`) to choose a
   `content_type` and apply the brand kit (voice/tone/rules).

2. **Generate** — `POST /generate` with `{ "content_type", "instructions", "save", "max_results" }`.
   The response returns `generated_text`, the `sources[]` used, and a `content_id` when `save` is true.
   To generate from a stored GEO prompt instead, use `POST /generate/prompt` (tool `generate_with_prompt`).

3. **Review / publish** — saved output enters the generated-content set (`senso generated-content list`);
   push it out with `senso engine publish` or hold it with `senso engine draft`.

## Rules
- Always pass `instructions`; empty instructions or content_type are rejected.
- Prefer `save: false` for previews; only save when the draft should enter the review/publish workflow.
- Report the `sources[]` Senso used so the generated content stays traceable to ground truth.
