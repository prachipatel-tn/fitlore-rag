# FitLore RAG project

The complete 112-file application source is in [FitLore Source Code.zip](./FitLore%20Source%20Code.zip). Download and unzip it, then open the FitLore folder and follow the setup below. The archive preserves all source directories and image assets. It excludes credentials, runtime databases, private screenshots and account billing details.

The measured 30-run comparison is also available as [quality-results.json](./quality-results.json). This is an educational local application; broad answer quality and latency still need improvement.

# FitLore

A local personal styling workspace and Week 2 RAG project. FitLore uses a personal brief, wardrobe metadata, sourced guidance and feedback to support an evidence-backed consultation. Default rehearsal makes zero provider calls. Real embedding and generation endpoints are gated; the local demo now has an explicitly enabled, bounded Nebius + Pinecone configuration.

## Run

```sh
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
# Only if .env does not already exist, copy .env.example to .env.
.venv/bin/python -m uvicorn app.main:app --host 127.0.0.1 --port 8000
```

Open http://127.0.0.1:8000. The first run seeds a fictional client and six sample garments. Edit or replace them. Records and small JPEG photos persist in `data/runtime/fitlore.sqlite3`. Do not commit this database, personal exports, evaluation reports or `.env`.

## Features

- Personal brief, budget, footwear and sleeve settings.
- Wardrobe add/edit/remove, availability, optional locally resized photos.
- Knowledge library: pasted text, HTTPS provenance links, PDF/TXT/Markdown upload (85 KB; 20 PDF pages; 30,000 extracted characters). No OCR or automatic web scraping.
- Versioned retrieval, evidence inspection, saved consultations and JSON exports.
- Owned-wardrobe combinations filtered by structured settings, with live RAG explanations when enabled.
- Reflection journal; feedback becomes retrievable evidence.
- Style stories: three authored demo journeys (townhall, creator launch, first conference talk), with credited editorial photographs, a continuous brief-to-look narrative, scoped evidence search and saved sample plans. Demo wardrobes and feedback are isolated from personal retrieval.
- The Bigger Picture: a navigable capability diagram connecting personal context, wardrobe, knowledge, consultation and feedback, plus the separate agency workspace.
- Architecture/provider readiness screen and 12 local retrieval smoke checks.
- **Projects**: six research-supported workflow templates spanning individuals, creators, image consultants, boutiques, designer dressing and brand events. Each has its own brief, source notes, resources, task dependencies, budget estimate and role previews. Templates start with explicitly fictional details.
- Project resources distinguish owned pieces, purchases, gifts, loans and services. Add suppliers and tasks, record confirmations, inspect blockers and export a project. Resource, brief and evidence changes mark affected completed decisions for review, including downstream handoffs. No booking, payment or external messaging occurs.
- Project evidence search is scoped to one project and uses local keyword retrieval. It does not generate an AI answer or consume provider credits. The existing personal live RAG path remains separate and requires explicit cloud gates.
- `/inspiration`: credited fashion photography. `/studio`: retained agency demo with role views, looks, sketch, sources, approvals and finance estimates.

## RAG architecture

Browser → FastAPI → SQLite documents → 240-word chunks / 40-word overlap → Nebius embeddings via LlamaIndex → Pinecone vectors (or local SQLite) → vector and keyword reciprocal-rank fusion → explicit brief/wardrobe context → Nebius cited JSON generation → citation-ID, exact source-excerpt and freshness validation → saved consultation → feedback.

Personal and agency corpora are isolated. Chunk IDs include document identity, revision, offset and content; a fingerprint also covers metadata. Source changes invalidate the live index. Only text metadata is sent to the model, not wardrobe photos. Built-in guidance consists of short application-authored summaries with original links, not full articles, endorsements or licensed programmes.

Rehearsal uses keyword retrieval and rules, not semantic search or AI generation. Outfit composition is rule-based in both modes; live RAG provides evidence-based reasoning. The filters do not establish aesthetic quality, physical fit or compliance with every free-text preference. Unknown prices, stock and measurements remain unknown. Valid source IDs alone do not prove factual support.

## Enable live endpoints only when ready

Configure NEBIUS_API_KEY, NEBIUS_MODEL, NEBIUS_EMBEDDING_MODEL and optionally NEBIUS_BASE_URL in `.env`. Use model IDs available to your account. Never paste keys into chat.

Both PATCHBRIEF_ALLOW_CLOUD=true and FITLORE_RAG_ALLOW_CLOUD=true are required. The first variable is retained for compatibility. Verify credit-only billing and top-up settings before enabling. Restart the server, finish all source edits, build the index once from **Connections & evaluation**, then choose **Live RAG consultation**.

Shared persisted attempt ceilings: **1 ingestion, 1 hosted upload, 10 retrievals, 10 generations**, including failures. An operator-only, audited one-time ingestion retry can add one attempt after the first attempt; it never resets the ledger and is never called by error handlers. These are not dollar caps and cannot read balances. Re-indexing changed data requires an explicit allowance decision after the initial ingestion. No automatic reset, top-up or retry. Do not delete usage records to bypass limits.

The selected providers are Nebius (generation and embeddings) and Pinecone (hosted vector storage). Set FITLORE_VECTOR_STORE=pinecone, PINECONE_INDEX and FITLORE_PINECONE_NAMESPACE to use an existing cosine index matching the embedding dimensions. The personal path reuses cached embeddings when uploading, stores only vectors and source IDs remotely, and fingerprints namespaces by corpus and model. SQLite retains source text and images. Pinecone receives only vectors and source IDs; Nebius receives selected text evidence, never wardrobe photos. Index provisioning is a separate explicit setup action, never automatic in application routes. Automatic LangSmith tracing remains disabled. Optional requirements-eval.txt supports a bounded explicit evaluation export with a verified $0 trace-spend cap and feedback retention extension disabled. Cloud calls require verified credit-only billing; local attempt ceilings cannot enforce provider billing settings.

## Test and evaluate

```sh
.venv/bin/python -m pytest -q
.venv/bin/python -m scripts.evaluate_fitlore
# Only after explicit activation and indexing; uses up to five live questions:
.venv/bin/python -m scripts.evaluate_fitlore --live --limit 5
```

The 15-question set is `examples/fitlore/evaluation.json`. Live reports contain context, generated claims, latency and a CSV for manual support review. Faithfulness remains null until reviewed. The 12-case local test is lexical retrieval only. Targets: ≥95% supported factual claims and p95 <15 seconds; both unmeasured until live testing. A five-question run cannot establish a stable production p95.

## Scope

Complete local project workflow, not production multi-tenant SaaS. No accounts, tenant isolation, live retailer feeds, payments, social publishing or autonomous purchasing. Studio role selectors are demonstrations, not authentication. Bind to loopback; use fictional data for shared demos. Optional PATCHBRIEF_ACCESS_TOKEN gates APIs; the personal UI accepts it in page memory, but it does not replace user accounts.

Legacy PatchBrief routes remain for compatibility, not as part of this submission. Older README/demo are preserved under docs. See `docs/submission.md`, `docs/demo-script.md` and `docs/fitlore.md` for project materials.

## Navigation

Discover contains photographic style stories. My studio groups the brief, styling session and sources. The brief saves forward to Wardrobe, then Style these pieces opens the session. Saved looks retains plans with stable links that survive refresh. Browser Back/Forward restores the previous view. How it works, provider controls and the agency demo are available from the footer. Editorial photographs are clearly distinguished from actual wardrobe photos.

Projects opens the workflow collection. Choose a scenario, set a date and create a local project. **Team plan** shows handoffs; **Pieces & people** records supply and service confirmations; **Sources & research** holds project evidence and workflow references. Role previews focus responsibilities but are not authentication or private vendor portals. Dates are day-level planning dates, not timed calendar reservations. Estimated fees exclude unrecorded taxes and charges.

## Rehearse a connected project

1. Open **Projects**, choose **A collaboration that feels like you**, and create a local project.
2. In Team plan, mark the agreement review complete. The creator's look approval still waits for garment confirmations.
3. In Pieces & people, record confirmations for the jacket and top, including notes and ready dates before the approval date.
4. Return to Team plan. Complete the look approval and fitting.
5. Change the jacket to unavailable. The look approval and fitting now need review; downstream work is blocked. No replacement is invented automatically.
6. Search **jacket** in Sources & research to see the current record and relevant brief passages. Export the project to preserve its state.

The six workflow patterns have public source references in the app, checked October 17, 2026. Source-backed patterns are not evidence of demand, guaranteed differentiation or independently verified suppliers. Aisle Planner, StudioBinder and Event Temple already document broad coordination; FitLore's cross-workflow change checks are a differentiation hypothesis to test.

## Recording and full test results

Open http://127.0.0.1:8000/static/demo-guide.html for the timed recording route and exact click paths for the three style stories, all six project workflows, supplier and task extensions, consultant approvals, sketching, finance and analytics. Six additive fictional projects named Recording demo are prepared in the current local workspace; the personal live index is preserved. The downloadable companion is web/demo-steps.txt.

The expanded release suite passed 145 tests. Browser checks covered 42 desktop views with no broken images or horizontal overflow observed. An isolated replay tested recall, blocked premature completion, recovery, complete task traversal and export for all six workflows. See output/full-test-results.json, output/full-test-results.xml and output/workflow-recovery-results.json. Live AI quality findings include irrelevant shopping suggestions and redundant missing-information questions; a rejected photo-assessment response was replaced by an explicit local capability boundary. These results do not establish production readiness or broad model faithfulness.

Images are bundled under web/images and original sources are retained in web/images/credits.json. Uploaded wardrobe photos take priority; fallback category references are labelled and are not photos of the named owned item.

## Completed live quality comparison

Fifteen questions were attempted once before and once after a prompt/schema correction, using an unchanged corpus. Accepted answers improved from 9/15 to 14/15; strict assistant-reviewed rubric passes improved from 4/15 to 6/15. Mean rubric scores were 40% and 60%. Revised observed p95 was 20.126 seconds, above the 15-second target. Exact factual excerpts matched their cited text (33/33 revised accepted claims); that is not an independent faithfulness measure and excludes suggestions. See /static/quality-report.html and web/quality-results.json. Two explicit audited grants added 15 retrieval/generation attempts each; the total ceilings are now 40 each, with prior usage preserved and no extra indexing/upload allowance. Thirty base traces plus rubric feedback were exported to LangSmith with extend_trace_retention=False; the trace cap remains $0.

The local four-page report is in output/submission/FitLore Week 2 Project Report.docx. Source packaging is scripts/package_submission.py; it excludes .env, runtime databases and output screenshots. Video recording and a hosted code link remain to be completed.
