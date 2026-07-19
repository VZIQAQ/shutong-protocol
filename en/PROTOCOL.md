# ShuTong Protocol — Protocol Overview

> **Protocol**: ShuTong Protocol
> **Version**: V0.1
> **Date**: 2026-07-20
>
> Cognitive-layer human-AI collaboration protocol.
> Addresses a different problem domain than CHAP (collaboration-layer protocol, arXiv:2606.09751v1).

## Positioning

ShuTong Protocol defines **how information flows, how uncertainty is handled, how memory is consolidated, and how context is orchestrated** in deep human-AI collaboration.

It is not another RAG framework, nor another Agent architecture. It is a **protocol** — defining rules for the **cognitive layer**, beyond the collaboration layer.

## Core Thesis

> Constrain probabilistic models with deterministic rules. Transform fuzzy intent into executable deterministic contracts.

## Three-Layer Architecture

| Layer | Property | Responsibility |
|-------|----------|----------------|
| Selection Layer | Probabilistic | LLM generates cognitive externalization, intent recognition, query phrasing |
| Rule Layer | Deterministic | Structure validation, source annotation validation, execution interception |
| Information Layer | Absolutely Deterministic | User-confirmed facts stored verbatim, zero compression, traceable |

## Four Components

| Component | Core Idea | Verification Status |
|-----------|-----------|---------------------|
| Query Mechanism | Stop and ask when uncertain; model exposes assumptions; rule engine validates structure | ✅ Verified |
| Context Orchestrator | Four-level attention hierarchy, different compression rules per level | 📝 Protocol Design |
| Memory Domain | Hot/warm/cold layers, tag-based exact matching, dream consolidation | 📝 Protocol Design |
| Probabilistic Determinism | Selection can be probabilistic, transfer must be deterministic | ⚠️ Partially Verified |

## Protocol Documentation Index

| Document | Content |
|----------|---------|
| [probabilistic-determinism.md](V0.1/03-probabilistic-determinism.md) | Epistemological foundation of probabilistic determinism; rationale for rejecting confidence scores |
| [query-mechanism.md](V0.1/02-query-mechanism.md) | Query mechanism: rule engine, cognitive contract, preview format |
| [attention-hierarchy.md](V0.2-draft/05-attention-hierarchy.md) | Context orchestrator: four-level attention hierarchy, fragment encoding spec |

## Relationship with CHAP

CHAP and ShuTong Protocol address completely different problems:

| Dimension | CHAP | ShuTong Protocol |
|-----------|------|------------------|
| Focus | Collaboration layer: who made what decision, how to audit | Cognitive layer: what to do when AI is uncertain, how to prevent information loss |
| Core Problem | Task lifecycle, approval workflows, evidence chains | Query mechanism, context orchestration, memory domain, probabilistic determinism |
| Model Role | Not discussed (left to application layer) | **Core claim**: models should be embedded in decision nodes |

CHAP does not cover the cognitive layer. ShuTong Protocol does not cover the collaboration layer. They can coexist in the same system.

## Reference Implementation

ShuTong is the first verification demo of ShuTong Protocol. See [README.md](README.md)
