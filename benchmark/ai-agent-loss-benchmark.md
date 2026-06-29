# AI-Agent Loss Benchmark v0

*An open, structured log of real AI-agent and AI-assistant failures — built on the [Agent Loss Record (ALR) schema](../schema/agent-loss-record-schema.md).*

> **What this is:** 18 real, independently-documented incidents where a deployed AI system caused (or risked) a real loss, each structured into the same insurance-shaped format so they can be **compared**. The goal is an open standard the field maps onto — not a definitive index. Contributions welcome.
>
> **Honesty banner:** every row is `source_type: PUBLIC_INCIDENT`. Public reporting gives failure-mode, trigger, and rough severity — it does **not** reliably give the exposure base (volume), the verified control state, or the paid-loss $. Only ~5 of 18 have a hard dollar figure. Those blanks are the point (see section 4). `measured` = real award/settlement; `estimated` = reported but unpriced; `modeled` = severity inferred.
>
> Sources: AI Incident Database (AIID), court/tribunal/EEOC records, and primary press. Maintained by Kyle Mapue · kyle@mapuekyle.com.

## 1. The headline findings (what 18 rows already show)

**1. One control gap dominates.** **8 of 18** failures trace to a missing **output guardrail / grounding**. By far the most common single point of failure.

**2. Companies find out from the outside.** Detection was **external — a user, the press, researchers, or a court — in ~13 of 18.** Internal monitoring caught almost nothing.

**3. The realised losses are still small; the potential ones aren't.** Hard dollar figures are tiny (C$812, US$5k, US$365k) — but the set already contains a **~64M-record breach**, **~300k+ exposed private conversations**, and a **settled wrongful-death suit**. The market has mostly seen near-misses. The large *financial* catastrophe hasn't landed yet.

**4. Shared substrate = correlated risk.** Most incidents ride a general-purpose frontier LLM. One model-level flaw is therefore a *correlated* exposure across unrelated companies.

**5. The dominant failure modes are mundane, not exotic.** Hallucination, prompt injection, and missing human-approval-on-consequential-actions account for most of the set.

## 2. Failure-mode & missing-control distribution

| Failure mode | Count | Most common missing control |
|---|---|---|
| Hallucination / harmful advice | 5 | output guardrail / grounding |
| Misaligned / harmful output | 3 | output guardrail / safety filter |
| Data exposure / exfiltration | 3 | access control / DLP / egress |
| Unsafe autonomous action (agentic) | 2 | sandbox / approval gate / least-privilege |
| Goal hijack / prompt injection | 2 | input guardrail |
| Reliability / resource overload | 2 | rate-limit / human fallback |
| Biased decisioning | 1 | fairness testing |

## 3. The corpus (T1 fingerprints — the poolable, secrets-free unit)

| # | Incident (yr) | Auton. | Failure mode | Trigger | Missing control | Band | Conf. | Detection |
|---|---|:--:|---|---|---|:--:|---|---|
| 1 | Replit agent wiped prod DB (2025) | A4 | unsafe action + repudiation | ignored code-freeze | dev/prod isolation, approval | S2 | est. | user |
| 2 | PocketOS/Cursor deleted DB + backups in 9s (2026) | A4 | unsafe autonomous action | obstacle-removal | access control / least-priv | S3 | est. | user |
| 3 | Samsung source-code leak to ChatGPT (2023) | A0 | data exfiltration | routine use | DLP / egress control | S3 | mod. | internal |
| 4 | McDonald's "Olivia" hiring bot breach (2025) | A2 | data exposure | weak secrets + IDOR | access control / secrets mgmt | S3 | est. | researchers |
| 5 | Grok private chats exposed via Share (2025) | A1 | data exposure | insecure feature design | privacy-by-design review | S3 | est. | press |
| 6 | Cursor "Sam" invented login policy (2025) | A3 | cascading hallucination | normal question | output guard / human review | S1 | est. | press |
| 7 | Chevy dealer bot "$1 Tahoe" (2023) | A2 | goal hijack | prompt injection | input guardrail | S0* | mod. | press |
| 8 | NYC MyCity told businesses to break law (2024) | A2 | cascading hallucination | normal question | output guard / grounding | S1 | mod. | press |
| 9 | NEDA "Tessa" harmful ED advice (2023) | A2 | harmful advice | normal question | safety guardrail | S2 | mod. | users |
| 10 | DPD chatbot jailbroken to swear (2024) | A2 | goal hijack | prompt injection / jailbreak | input guardrail | S0* | mod. | user/press |
| 11 | Grok antisemitic / violence outputs (2025) | A1 | misaligned output | guardrail removed in config | output guard / safety + change-ctrl | S2 | mod. | press |
| 12 | Google Gemini image-gen pulled (2024) | A1 | misaligned output | normal use | output eval / red-team | S2 | mod. | press |
| 13 | McDonald's IBM drive-thru runaway orders (2024) | A3 | resource overload | normal use | rate-limit / human fallback | S1 | est. | operational |
| 14 | Klarna AI support rollback (2025) | A3 | reliability degradation | normal use | quality bar / human fallback | S1 | est. | internal |
| 15 | Air Canada bereavement-fare hallucination (2024) | A2 | cascading hallucination | normal question | output guard / grounding | S0 | **meas.** | tribunal |
| 16 | Mata v. Avianca — fake case citations (2023) | A0 | cascading hallucination | routine use | output verification | S1 | **meas.** | court |
| 17 | iTutorGroup age-biased hiring filter (2023) | A2 | biased decisioning | design | fairness testing | S2 | **meas.** | EEOC |
| 18 | Character.AI teen-harm suits (2024) | A3 | harmful output | normal use | safety guardrail / age-gating | S4 | **meas.** | lawsuit |

\* realised loss low (not honoured / reputational); *potential* severity higher. Bands per the [schema](../schema/agent-loss-record-schema.md) section 4 (S0 <$1k … S5 >$250M).

## 4. What this benchmark cannot tell you — and why that matters

Public data fills the *shape* of risk but not the *price*. Across all 18 rows, four fields are systematically missing:

| Field needed to price risk | In public data? |
|---|---|
| Exposure base (decisions/volume — the denominator) | never |
| Paid-loss $ (true severity) | only 4–5 of 18 |
| Verified control state at failure | inferred from press |
| Frequency per exposure | impossible from anecdotes |

**These four blanks can only be filled from real claims data held by insurers.** This benchmark is the public, `modeled` foundation; the priced layer is built on top of it, with the people who hold claims.

## 5. How to read / contribute

- Each row is a **T1 fingerprint**: the failure pattern with prompts, payloads, names, and business data stripped — comparable across organisations without exposing anyone's secrets.
- The full schema is in [agent-loss-record-schema.md](../schema/agent-loss-record-schema.md). Five detailed worked records are in [seed-corpus.md](seed-corpus.md).
- To contribute a public incident, open an issue or a pull request.

*Benchmark v0 — 18 public incidents, mostly `modeled`/`estimated`. The value isn't the count; it's that real failures fit one comparable shape, the patterns are already legible, and the blanks map exactly onto what only claims data can provide.*
