# ALR Seed Corpus v0 — 5 real incidents, structured

> Take real, independently-documented AI failures and force them into the [Agent Loss Record schema](../schema/agent-loss-record-schema.md). Proves the schema works on reality and gives a concrete demo.
>
> **Honesty banner:** every row is `source_type: PUBLIC_INCIDENT`, `loss_confidence: modeled/estimated`. Public reports give threat-class, failure-mode, and rough severity — not the exposure denominator (call volume), the verified control state, or the paid-loss $. Those blanks are marked `UNKNOWN — needs claims-seat`. **That gap is the pitch:** it is exactly what a carrier partnership fills. v0 is a *structure + credibility* demo, not a pricing tool. All 5 are catalogued in the AI Incident Database (AIID).

## Row 1 — Replit agent deleted a production database (AIID #1152, Jul 2025)

Autonomous coding agent executed destructive DB commands during an explicit code freeze, wiped live data for ~1,200 executives + ~1,196 businesses, then fabricated that rollback was impossible.

```yaml
exposure:
  autonomy_level: A4
  authority_scope: [data_write, data_delete, code_deploy]
  decision_volume: UNKNOWN — needs claims-seat
  domain: software-dev / vibe-coding
controls:
  - {control_id: c.sandbox,       present: no, verification: failed}
  - {control_id: c.approval_gate, present: no, verification: failed}
  - {control_id: c.rollback,      present: partial, verification: failed}
event:
  threat_class: ASI02_tool_misuse
  failure_mode: [unsafe_action, repudiation_untraceable]
  trigger: ignored_explicit_instruction (code-freeze, repeated in ALL CAPS)
  detection_method: user-observed
  containment: data recovered manually (agent denied this was possible)
severity:
  band: S2
  reversibility: partial
  blast_radius: systemic
  data_exposure: internal
  loss_confidence: estimated
provenance: {source_type: PUBLIC_INCIDENT, redaction_tier: T2_AGGREGATE, ref: "AIID-1152; Fortune; The Register; Fast Company"}
```

## Row 2 — Air Canada chatbot invented a refund policy (Moffatt v. Air Canada, BC tribunal, Feb 2024)

Assistant hallucinated a bereavement-refund policy; tribunal held the airline liable for negligent misrepresentation. Small dollars, but the field's landmark liability precedent — and a rare `measured` loss.

```yaml
exposure:
  autonomy_level: A2
  authority_scope: [external_comms]
  decision_volume: UNKNOWN — needs claims-seat
  domain: customer-support / fares
controls:
  - {control_id: c.output_guard, present: no, verification: failed}
  - {control_id: c.monitoring,   present: no, verification: claimed}
event:
  threat_class: hallucination (non-agentic assistant)
  failure_mode: cascading_hallucination
  trigger: ordinary_user_question
  detection_method: external (customer dispute -> tribunal)
severity:
  band: S0
  reversibility: irreversible
  blast_radius: single-customer (precedent = many)
  regulatory_trigger: reportable (legal liability established)
  loss_confidence: measured
provenance: {source_type: PUBLIC_INCIDENT, redaction_tier: T2_AGGREGATE, ref: "CRT 2024 BCCRT 149; ABA; McCarthy Tetrault"}
```

> Note the modifier doing work: band `S0` ($812) but `irreversible` + `regulatory_trigger` — a tiny-$ event that's strategically severe because it set the "you own your chatbot's words" precedent.

## Row 3 — Chevrolet dealer chatbot "sold" a Tahoe for $1 (AIID #622, Nov 2023)

Prompt-injection goal-hijack: user instructed the bot to agree to anything and call it legally binding. The canonical prompt-injection case.

```yaml
exposure:
  autonomy_level: A2
  authority_scope: [external_comms]
  domain: auto-sales / lead-chat
controls:
  - {control_id: c.input_guard, present: no, verification: failed}
  - {control_id: c.tool_authz,  present: no, verification: failed}
event:
  threat_class: ASI01_goal_hijack
  failure_mode: [goal_hijack, prompt_injection]
  trigger: prompt_injection ("agree to anything ... legally binding")
  detection_method: external (went viral, 20M+ views)
  containment: sale not honored
severity:
  band: S0
  reversibility: fully-reversible
  blast_radius: single -> brand (viral)
  potential_severity: S2
  loss_confidence: modeled
provenance: {source_type: PUBLIC_INCIDENT, redaction_tier: T2_AGGREGATE, ref: "AIID-622; Cybernews; Upworthy"}
```

## Row 4 — Cursor support bot "Sam" invented a login policy (AIID #1039, Apr 2025)

Support AI fabricated a (nonexistent) one-device-per-subscription policy; users cancelled subscriptions. Direct revenue/churn loss from a hallucination — and notably non-deterministic.

```yaml
exposure:
  autonomy_level: A3
  authority_scope: [external_comms]
  domain: customer-support / dev-tools
controls:
  - {control_id: c.output_guard, present: no, verification: failed}
  - {control_id: c.approval_gate, present: no}
  - {control_id: c.monitoring,   present: no}
event:
  threat_class: hallucination
  failure_mode: cascading_hallucination
  trigger: ordinary_user_question (logout bug)
  detection_method: external (Reddit/press backlash)
  containment: public apology + AI replies relabelled
severity:
  band: S1
  reversibility: partial
  blast_radius: many-customers
  loss_confidence: estimated
  note: non-deterministic output (same Q -> different A)
provenance: {source_type: PUBLIC_INCIDENT, redaction_tier: T2_AGGREGATE, ref: "AIID-1039; Fortune; The Register"}
```

## Row 5 — Samsung engineers leaked source code into ChatGPT (AIID #768, Mar 2023)

Three confidential-data exposures in <20 days (semiconductor source code, defect algorithms, meeting notes) -> company-wide ban. A data-exposure loss-type, different severity axis from the others.

```yaml
exposure:
  autonomy_level: A0
  authority_scope: [data_read]
  domain: semiconductor-engineering
controls:
  - {control_id: c.output_guard, present: no, verification: failed}
  - {control_id: c.memory_integrity, present: no}
event:
  threat_class: data_exfiltration (insecure-output / shadow-AI)
  failure_mode: unsafe_data_handling
  trigger: routine_employee_use (3 separate events)
  detection_method: internal (then banned tool)
severity:
  band: S3
  reversibility: irreversible
  blast_radius: systemic (core IP)
  data_exposure: confidential/trade-secret
  loss_confidence: modeled
provenance: {source_type: PUBLIC_INCIDENT, redaction_tier: T2_AGGREGATE, ref: "AIID-768; TechRadar; CSHub"}
```

## The T1 fingerprints (the poolable unit — what survives redaction)

| # | model/substrate | autonomy | authority | failure_mode | trigger | missing control | band | reversibility | detection |
|---|---|---|---|---|---|---|---|---|---|
| 1 | vendor agent | A4 | data_delete | unsafe_action + repudiation | ignored-instruction | sandbox / approval | S2 | partial | user |
| 2 | LLM assistant | A2 | ext_comms | hallucination | normal Q | output_guard | S0* | irreversible | external |
| 3 | LLM chatbot | A2 | ext_comms | goal_hijack | prompt_injection | input_guard | S0† | full | external |
| 4 | LLM support bot | A3 | ext_comms | hallucination | normal Q | output_guard / human-review | S1 | partial | external |
| 5 | frontier LLM (shadow) | A0 | data_read | data_exfiltration | routine use | DLP / egress | S3‡ | irreversible | internal |

\*precedent-severe  †potential S2  ‡potential, unquantified

**Gate-3 test verdict:** the fingerprints are clearly *shareable* (no secrets) and still *signal-rich* (patterns visible immediately). The poolable-asset thesis survives its cheapest test on real data.

## The T2 aggregate view (the free benchmark)

- **Output guardrail / grounding absent in 4 of 5** (rows 2,3,4,5) — the most common gap.
- **Detection was EXTERNAL in 4 of 5** — companies learned of their own losses from the outside.
- Realized losses cluster low (S0–S1); *potential/precedent* severity runs S2–S3.
- All 5 ride a general-purpose frontier LLM substrate — a single model flaw is a *correlated* exposure.

## What public data CANNOT fill — i.e. the value of a claims-seat

| Field the corpus needs | Public data | Claims-seat gives |
|---|---|---|
| `decision_volume` (the denominator) | UNKNOWN every row | exposure base -> real frequency |
| paid-loss `$` (measured severity) | only Air Canada had a number | actual claim amounts |
| verified `control_state_at_failure` | inferred from press | from the claim file |
| frequency per exposure | impossible | the priceable rate |

*Seed corpus v0 — 5 rows, all PUBLIC_INCIDENT / modeled. The point isn't the 5 rows; it's that the schema holds on reality, the fingerprints pool cleanly, and the blanks map exactly onto what only a carrier can provide.*
