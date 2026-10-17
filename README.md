# FitLore

A personal styling RAG application built for The Gen Academy Week 2. FitLore connects a person's wardrobe, event brief, preferences and source evidence to a styling decision, with separate collaborative workflows for creators and consultants.

## Get the full source

Download [FitLore Source Code.zip](./FitLore%20Source%20Code.zip), unzip it, and open the **FitLore** folder. The archive contains all 112 project files with their directory structure preserved. Credentials, runtime databases, private screenshots and account billing details are excluded.

## Run locally

```sh
python3 -m venv .venv
.venv/bin/pip install -r requirements.txt
.venv/bin/python -m uvicorn app.main:app --host 127.0.0.1 --port 8000
```

Open http://127.0.0.1:8000. The default rehearsal uses no paid endpoints. Set up your own `.env` from `.env.example` only if it does not already exist. Verify provider billing controls before enabling either cloud gate.

## RAG pipeline

Personal brief, wardrobe metadata, guidance, event documents and feedback → 240-word chunks with 40-word overlap → Nebius embeddings through LlamaIndex → Pinecone → semantic and keyword rank fusion → Nebius cited JSON → exact-excerpt and freshness checks.

Creative styling suggestions are separated from factual excerpts. Photos are references or local uploads; the model does not analyze them. The six project templates demonstrate resources, approvals, loans, handoffs and decision invalidation locally.

## Measured evaluation

The [complete summary](./quality-results.json) covers the same 15 questions before and after a prompt/schema correction, for 30 live attempts on an unchanged corpus.

| Measure | Baseline | Revised |
| --- | ---: | ---: |
| Accepted responses | 9/15 | 14/15 |
| Strict assistant-reviewed rubric passes | 4/15 | 6/15 |
| Mean rubric score | 40% | 60% |
| Observed p95 | 12.231s | 20.126s |
| Accepted factual excerpts matching cited text | 24/24 | 33/33 |

The rubric review was performed by Codex, not an independent human or paid judge. Excerpt matching excludes suggestions and does not establish source truth or relevance. One citation failure remains; answer relevance and the 15-second latency target still need improvement. The automated suite passed **145 tests**.

Thirty evaluation traces and rubric feedback were exported to LangSmith with a $0 trace-spend cap and retention extension disabled. Application-wide tracing remains off. Model and vector calls use explicit, persistent attempt limits and no automatic retries or quota resets.

## Demo materials

After launching locally, open **/static/demo-guide.html** for the five-minute route and exact clicks across all six workflows. **/static/quality-report.html** explains each evaluation case. The video recording and final submission are separate steps.

This is a local educational project. It does not provide production authentication, tenant isolation, verified shopping inventory, vision analysis, bookings or payments. Image source credits and third-party rights notices are in **web/images/credits.json** inside the source archive.
