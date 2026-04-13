# Vulcan OmniPro 220 — Multimodal assistant (Prox challenge)

Fork: **isabelccc/prox-challenge** — a local-first assistant that answers technical questions about the OmniPro 220 using the PDFs in `files/`, with **citations**, **interactive artifacts**, and an **offline retrieval benchmark**.

<img src="product.webp" alt="Vulcan OmniPro 220" width="360" /> <img src="product-inside.webp" alt="OmniPro 220 panel" width="360" />

## Quick start (under ~2 minutes after clone)

```bash
git clone git@github.com:isabelccc/prox-challenge.git
cd prox-challenge
cp .env.example .env # add ANTHROPIC_API_KEY
npm install
```

**One-time knowledge build** (downloads local embedding model ~23MB; optional Claude vision per page):

```bash
npm run extract
# Cheaper dev iteration:
# SKIP_VISION=1 npm run extract
```

**Run the UI:**

```bash
npm run dev
# http://localhost:3000
```

## What you get

- **Next.js 15 + TypeScript** chat UI (streaming SSE), voice input (Web Speech API), dark industrial styling.
- **Tool-using agent** (`@anthropic-ai/sdk`): `search_manual`, `get_specifications` (page-7 structured duty/current tables), `troubleshoot`, `generate_artifact` (HTML / SVG / Mermaid in a sandboxed iframe), `suggest_followups`.
- **Knowledge pipeline**: MuPDF → per-page text + PNGs → (optional) Claude vision descriptions → **local** `all-MiniLM-L6-v2` embeddings → **LanceDB** vector index.
- **Benchmarks**: `npm run eval:retrieval` checks that retrieval hits contain expected keywords (no LLM call).

## Reliability choices (why this should score well)

1. **Dual path for specs**: numeric duty/current questions hit structured JSON transcribed from **Owner's Manual p.7**, not “whatever retrieval returned”.
2. **Explicit polarity rules in the system prompt** for TIG vs Stick vs MIG sockets, aligned with **p.8 / p.24 / p.27**.
3. **Clarification-first behavior** when process, gas, or voltage is missing.
4. **Citations required** in the system prompt (document + page).
5. **Measurable retrieval quality** via `benchmark/benchmarks.json`.

## Commands

| Command | Purpose |
|--------|---------|
| `npm run dev` | Chat UI + API |
| `npm run build` | Production build |
| `npm run extract` | Build `data/knowledge.lance` + `data/images/*.png` |
| `npm run eval:retrieval` | Keyword checks on top-k retrieval |

## Project layout

- `app/api/chat` — SSE agent loop  
- `lib/agent/*` — tools, embeddings, LanceDB  
- `lib/data/specifications.ts` — structured p.7 specs  
- `scripts/extract-knowledge.ts` — PDF ingestion  
- `benchmark/benchmarks.json` — retrieval smoke tests  

## Limits / honesty

- **Artifacts** execute in a sandboxed iframe; complex Mermaid diagrams depend on CDN availability.
- **Vision** during extract improves figure-heavy pages; use full extract before submission demos.
- **Welding safety**: user must follow the manual and local codes — the assistant summarizes, it does not replace certified training.

## Original challenge

See the upstream Prox repo for full requirements: [prox-technologies/prox-challenge](https://github.com/prox-technologies/prox-challenge).
