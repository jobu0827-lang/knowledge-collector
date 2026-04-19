# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository state

This repo currently contains **only a blueprint document**, not an implemented system. There is no code, build system, tests, or dependencies yet. The single file is:

- `blueprint_knowledge-collector.md` — v5 spec (Korean) for a "Domain Knowledge Collector" targeting vehicle NVH / RNH engineering and driving-simulator subjective evaluation.

When the user asks to "start building" or "generate the initial structure," they mean executing **Phase 0–7 of the blueprint** (see `## 📦 요구 산출물` section). Expected initial deliverables land under a to-be-created `knowledge-base/` tree — see Phase 1 for the directory layout.

## Non-negotiable design axioms

The blueprint's top-level principle is **"Verified Facts Only"** with explicit epistemic classification. Any system, subagent, skill, or document generated in this repo MUST respect these hard rules (blueprint §A, §I, and 제약사항 10–15):

1. **Every factual claim carries a traceable source.** No source → cannot be written to the KB; goes to `50_evolution/gaps/` instead.
2. **Never invent formulas, numbers, abbreviations, authors, DOIs, or URLs.** Extract and cite only. "Similar-looking" formulas are forbidden.
3. **Hedge phrases** like "일반적으로", "흔히", "대체로" are banned as standalone — always pair with a source or an `EST` block.
4. **Single-source claims classify as `SSF`, never `VF`.** Promotion to `VF` requires a second independent source.
5. **Estimations (`EST`) require 5 fields**: `basis`, `method`, `confidence`, `verification-plan`, `expires`. An `EST` without an expiry is invalid.
6. **"I don't know" is the correct answer** when evidence is missing. Do not fill gaps with plausible-sounding guesses — log to `gaps/`.

The 7-tier epistemic classification is `VF | SSF | EC | EMP | EST | HYP | DEP`. Every knowledge document's frontmatter must carry `epistemic-status` plus source/verification metadata (see Phase 2 template in the blueprint).

## Architecture the blueprint prescribes

When generating the `knowledge-base/` tree, preserve these structural decisions — they encode the anti-hallucination strategy:

- **`00_meta/epistemics/`** holds the classification guide, source-reliability table, verification protocol, and anti-hallucination charter. These are load-bearing — other documents reference them.
- **`70_evidence/`** is deliberately separate from knowledge documents. It stores original-source excerpts, papers, and standards metadata so that citation integrity survives link-rot. The `excerpts/` subdir exists specifically so `citation-matcher` can verify claim↔source alignment.
- **`90_index/`** contains three master registers — `claim-index.md`, `source-index.md`, `estimation-register.md` — that enable cross-cutting audits (e.g., EST expiry sweeps).
- **`50_evolution/`** captures lifecycle events: ingestion logs, conflicts, gaps, meta-insights, and (critically) `hallucination-incidents/` as an immutable learning asset.

### Subagent roles (blueprint §1)

Generated subagents fall into four tiers. Anti-hallucination agents are the most load-bearing and must run inside every verification pipeline:

- **Domain experts**: nvh, vehicle-dynamics, tire, cae, ml-architect, realtime-sim, motion-cueing, perception-science.
- **Self-evolution**: research-scout, knowledge-curator, conflict-resolver, lifecycle-manager, meta-learner, gap-detector.
- **Rigor**: formula-auditor, glossary-guardian, symbol-consistency, derivation-verifier.
- **Anti-hallucination** (required gate): fact-verifier, source-auditor, hallucination-detector, citation-integrity, epistemic-classifier.

The verification pipeline (blueprint §G) is fixed-order: `research-scout → fact-verifier → source-auditor → hallucination-detector → domain-expert → formula-auditor + glossary-guardian → human reviewer`. Failure at any stage routes to `gaps/` — never partial-publish to KB.

### Motion Cueing source anchors (do not alter)

When writing MC content, use these exact seminal sources — they are pinned in the blueprint (§Motion Cueing 수식 세트):

- MC-1 CWF → Reid & Nahon (1985), UTIAS Report No. 296
- MC-3 Vestibular → Young & Oman (1969), Aerospace Medicine
- MC-4 MPC-MCA → Dagdelen et al. (2009), Control Engineering Practice
- MC-7 OMCT → Advani & Hosman (2007), AIAA

## Working conventions

- Primary language: **Korean**, with English technical terms in parentheses (per blueprint 제약사항 1).
- Use `ultrathink` for analysis and `ultraplan` for planning when executing blueprint phases (blueprint's explicit instruction).
- The blueprint recommends an **initial 30-day "Evidence-first operating mode"** — prioritize building `70_evidence/` and `source-index.md` before authoring knowledge documents. If the user asks you to start writing KB content on day 1, flag this trade-off.
- All EST entries need an `expires` date; set weekly review of the estimation register as the default cadence.
