# AI Knowledge Platform

A commercial-grade AI-assisted knowledge platform built 
around a multi-model orchestration pipeline, structured 
validation architecture, and human-in-the-loop review system.

This is not a prototype. It is designed to be maintainable, 
auditable, and extensible.

---

## Architecture

### Three-Phase Completion Pipeline

Evidence is gathered and validated through three models 
in sequence, each with a defined role:

**Phase 1 — Evidence Gathering**  
Perplexity Sonar (standard + pro tiers) with automatic 
upgrade logic based on evidence quality thresholds.  
PubMed NCBI E-utilities for high-safety domains.  
Vector DB lookup via ChromaDB for corpus-specific retrieval.

**Phase 2 — Analytical Draft**  
arcee-ai/trinity-large-thinking via OpenRouter.  
Locked prompt rules enforce effect size ceilings, 
evidence traceability, and dose-response field formats.  
Fallback: Claude Sonnet via Anthropic direct.  
120-second timeout with retry.

**Phase 3 — Logic Audit**  
moonshotai/kimi-k2.5 via OpenRouter.  
Structured JSON audit report with section-level severity 
ratings. Routing recommendation overridden by section 
severity fields when inconsistent.  
3-attempt retry with 0s/5s/15s backoff.  
Fallback: o3 via OpenAI direct.

---

## Key Design Decisions

**LLMs assist. They do not decide.**  
Safety checks, scoring, and activation decisions are 
deterministic. LLMs handle drafting, parsing, summarization, 
and structured evaluation — not logic.

**Versioned records, never overwritten.**  
Every pipeline run increments a version. Historical records 
are preserved as an audit trail.

**All weights and thresholds are configurable.**  
Nothing is hardcoded. Routing thresholds, evidence weights, 
and scoring parameters live in a config table.

**Human-in-the-loop review system.**  
AI triage generates confidence-scored reviewer cards 
(green/blue/yellow/red) with structured recommendations. 
Humans make final activation decisions.

---

## Review System

The curator review system includes:

- AI-generated triage cards with confidence scoring 
  (Kimi K2.5)
- Confidence color distribution across the full queue
- Bulk confirmation flow for high-confidence approvals
- Edit flow with AI-generated diff preview and mandatory 
  re-audit after edits
- Frontier disposition tier for records with thin evidence 
  but low downside risk — surfaced behind a tailored 
  disclaimer rather than discarded
- Hard reject path for genuinely dangerous records
- Immutable edit history audit trail

---

## Taxonomy System

800+ structured records tagged across two orthogonal 
dimensions using a junction table architecture:

- **Outcome dimension** — what goal does this record serve?
- **Modality dimension** — what type of intervention is this?

Concurrent AI tagging (N=10) with smart resume semantics, 
is_primary auto-correction, and version-based re-tagging 
for taxonomy updates. Partial UNIQUE indexes enforce 
data integrity at the DB layer.

---

## Stack

**Backend:** Python 3.11+, FastAPI  
**Databases:** PostgreSQL, ChromaDB  
**Frontend:** React 18, Vite, React Router  
**AI Models:** Perplexity Sonar, Trinity Large, 
Kimi K2.5, Claude Sonnet, o3  
**Infrastructure:** Docker, OpenRouter, Anthropic API, 
OpenAI API

---

## Engineering Discipline

- 582 tests (backend + frontend)
- Branch-based development, no direct main commits
- Docker for all database operations
- Checkpoint logging every 50 records in batch runs
- Configurable thresholds, never hardcoded
- Concurrent batch processing with retry and fallback logic

---

## Status

Active development. Pipeline complete. 
Review system complete. Taxonomy restructure complete.  
Phase 1 core runtime in progress.
