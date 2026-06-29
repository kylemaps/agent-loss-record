# The Agent Loss Record (ALR) — Standard v0.1

> The unit-of-account for AI-agent risk. An open, versioned, standards-mapped record that turns a messy agent failure into a **comparable, priceable, poolable** data point.
>
> Goal: be the format insurers, auditors, and (eventually) regulators map onto — the "Aaa/Baa" of AI-agent loss. The corpus is the asset; **this schema is the moat.**
>
> Status: v0.1 open draft. Maintained by Kyle Mapue (kyle@mapuekyle.com).

## 0. The one idea

Insurers can't price agent risk because every incident is described differently and none of it is structured like insurance data. Insurance data has a fixed shape:

```
   LOSS COST  =  FREQUENCY  ×  SEVERITY     ... over an EXPOSURE base, adjusted by CONTROLS
```

The ALR forces every agent failure — real paid claim, reported incident, public news event, or synthetic red-team finding — into that shape. Once everything is in that shape, it can be **compared** (benchmark) and **pooled** (rating).

Three things make a record an ALR and not just a log:
1. **Actuarial shape** — exposure / controls / event / severity, so it's priceable.
2. **Standards crosswalk** — every event tagged to OWASP-ASI / MITRE-ATLAS / AIUC-1, so it's interoperable and credible.
3. **Provenance + integrity envelope** — so a buyer trusts it (and it can be tamper-sealed and redacted for sharing).

## 1. The five objects

An ALR is a bundle of five linked objects. **Exposure + Controls** are observable today; **Event + Severity** are what the corpus accumulates; **Dependency** is what reinsurers pay most for.

```
  EXPOSURE UNIT      one assessed/insured agent deployment (the denominator)
      | has a
  CONTROL PROFILE    the rating factors — what moves price up/down
      | experiences
  LOSS EVENT(S)      one observed/synthetic failure (the numerator)
      | scored by
  SEVERITY & BAND    THE UNIT OF ACCOUNT (the comparable grade)
      | correlated via
  DEPENDENCY MAP     systemic layer — shared model/tool/vendor = correlated loss
  + PROVENANCE & INTEGRITY ENVELOPE wraps every object
```

### Object 1 — EXPOSURE UNIT (the denominator)
Fillable from a deployment review today — no loss history needed.

| Field | Meaning | Example |
|---|---|---|
| `exposure_id` | stable id | `exp_7f3a` |
| `autonomy_level` | A0–A5 (see section 2) | `A3` |
| `authority_scope[]` | what it can affect, each with a `value_at_risk` | `money_move:$500/action`, `external_comms` |
| `decision_volume` | the exposure base | `50,000 / month` |
| `value_at_risk_max` | worst single-action $ | `$500` |
| `model` / `model_family` | engine + family | `gpt-x-2026-03` / `gpt-x` |
| `framework` | orchestration layer | `langgraph` |
| `domain` | business function | `customer-support refunds` |
| `data_sensitivity` | PII / PHI / financial / none | `financial + PII` |

### Object 2 — CONTROL PROFILE (the rating factors)
Each control present + how verified.

| Field | Meaning |
|---|---|
| `control_id` | from the control catalog (section 3) |
| `present` | yes / no / partial |
| `verification` | `claimed` / `attested` / `tested` / `failed` |
| `effectiveness` | 0–4 grade |
| `aiuc1_domain` | crosswalk to AIUC-1 domain |
| `owasp_mitigation` | crosswalk to OWASP-ASI mitigation |

### Object 3 — LOSS EVENT (the numerator)
One failure, real or synthetic — same shape.

| Field | Meaning |
|---|---|
| `event_id` | id |
| `occurred_at` | timestamp (or `synthetic`) |
| `threat_class` | OWASP-ASI id (e.g. `ASI01 Goal Hijack`, `ASI02 Tool Misuse`) |
| `atlas_technique` | MITRE ATLAS technique id |
| `failure_mode` | from section 4 |
| `trigger` | what set it off |
| `trace_ref` | pointer to the captured run / cassette (sealable) |
| `control_state_at_failure[]` | which controls were on / off / bypassed |
| `detection_method` | how it was caught |
| `time_to_detect` | dwell time |
| `containment` | stopped? reversed? partial? none? |

### Object 4 — SEVERITY & LOSS BAND (the unit of account)

Financial severity bands:

| Band | Label | Realized/Potential loss |
|---|---|---|
| `S0` | Negligible | < $1k |
| `S1` | Minor | $1k – $50k |
| `S2` | Moderate | $50k – $1M |
| `S3` | Major | $1M – $25M |
| `S4` | Severe | $25M – $250M |
| `S5` | Catastrophic / systemic | > $250M |

Modifier dimensions (attached to the band):

| Dimension | Scale |
|---|---|
| `reversibility` | fully-reversible → partially → irreversible |
| `blast_radius` | single-record → single-customer → many-customers → systemic |
| `detection_lag` | seconds → hours → days → undetected-until-external |
| `data_exposure` | none → internal → PII/PHI → regulated breach |
| `regulatory_trigger` | none → reportable → fine-bearing |
| `loss_confidence` | `measured` (paid claim) / `estimated` (reported) / `modeled` (synthetic) |

### Object 5 — DEPENDENCY / AGGREGATION MAP (the systemic layer)

| Field | Meaning |
|---|---|
| `shared_model_family` | e.g. all on `gpt-x` |
| `shared_framework` | e.g. `langgraph` |
| `shared_tool_or_mcp` | a common tool/MCP server |
| `shared_vendor` | a common agent vendor / platform |
| `correlation_tags[]` | the accumulation keys an insurer aggregates on |

### Cross-cutting — PROVENANCE & INTEGRITY ENVELOPE

| Field | Meaning |
|---|---|
| `source_type` | `REAL_CLAIM` / `INCIDENT_REPORTED` / `PUBLIC_INCIDENT` / `REDTEAM_SYNTHETIC` |
| `contributor_id` | pseudonymous |
| `schema_version` | `ALR-0.1` |
| `redaction_tier` | `T0_RAW` / `T1_FINGERPRINT` / `T2_AGGREGATE` (see section 5) |
| `content_hash` | Merkle root over the record + trace |
| `signature` | Ed25519 |
| `timestamp_proof` | OpenTimestamps proof |

## 2. Autonomy scale (A0–A5)

| Level | Definition |
|---|---|
| A0 | Suggest-only; a human executes everything |
| A1 | Human approves every action before it runs |
| A2 | Auto-executes low-risk; human approves high-risk |
| A3 | Auto-executes within policy; human monitors |
| A4 | Fully autonomous in-domain; human handles exceptions only |
| A5 | Fully autonomous; no human in the loop |

## 3. Control catalog (rating factors) — mapped to AIUC-1 / OWASP

| `control_id` | Control | AIUC-1 domain | OWASP-ASI |
|---|---|---|---|
| `c.input_guard` | Input/prompt-injection filtering | Security | ASI01/ASI02 |
| `c.output_guard` | Output PII/policy filtering | Data & Privacy | — |
| `c.tool_authz` | Tool-call authorization / least privilege | Security | ASI02/ASI03 |
| `c.approval_gate` | Human approval gate (by action class) | Accountability | ASI01 |
| `c.rate_budget` | Rate / budget / loop limits | Reliability | ASI04 |
| `c.sandbox` | Environment isolation | Security | ASI02 |
| `c.rollback` | Reversibility / rollback mechanism | Reliability | — |
| `c.monitoring` | Anomaly detection / alerting | Reliability | ASI08 |
| `c.memory_integrity` | Memory-poisoning controls | Safety | ASI05 |
| `c.redteam_recency` | How recently red-teamed | Accountability | — |

## 4. Failure-mode taxonomy

`goal_hijack` · `tool_misuse` · `privilege_compromise` · `memory_poisoning` · `cascading_hallucination` · `resource_overload` · `misaligned_deceptive` · `repudiation_untraceable` · `unsafe_delegation` · `prompt_injection` (usually the trigger enabling the others).

## 5. Redaction tiers (the shareability ladder)

| Tier | Contains | Who sees it |
|---|---|---|
| `T0_RAW` | full trace, prompts, payloads, business data | single-tenant only — never pooled |
| `T1_FINGERPRINT` | threat_class, failure_mode, control_state, severity band+modifiers, model_family, dependency tags — no raw prompts/business data | the poolable unit |
| `T2_AGGREGATE` | cohort statistics only | fully public — the benchmark |

Strategy: **pool T1, publish T2, keep T0 with the client.**

## 6. The rating output

- **Expected Annual Loss (EAL)** = sum over threat-classes of `frequency(class | exposure, controls) × severity(class)`.
- **An ALR grade** — a single letter/number (e.g. `AL1`…`AL9`) from EAL relative to value-at-risk.
- **A benchmark percentile** — "worse than 78% of comparable deployments."

Pre-corpus: frequency from red-team hit-rate + public base rates → grades are `modeled`. Post-corpus: frequency from real pooled claims → grades become `measured`.

## 7. Worked example

```yaml
exposure:
  exposure_id: exp_7f3a
  autonomy_level: A3
  authority_scope:
    - {kind: money_move, value_at_risk: 500}
    - {kind: external_comms}
  decision_volume: "50000/month"
  model: gpt-x-2026-03 ; model_family: gpt-x
  framework: langgraph
  domain: customer-support-refunds
  data_sensitivity: [financial, PII]
controls:
  - {control_id: c.input_guard,   present: yes, verification: failed, effectiveness: 0}
  - {control_id: c.approval_gate, present: partial, verification: tested, effectiveness: 1}
  - {control_id: c.rate_budget,   present: no,  verification: claimed, effectiveness: 0}
  - {control_id: c.rollback,      present: no,  verification: claimed, effectiveness: 0}
event:
  event_id: ev_91c2
  threat_class: ASI01_goal_hijack + ASI02_tool_misuse
  failure_mode: goal_hijack
  trigger: prompt_injection_in_user_message
  control_state_at_failure: [input_guard:bypassed, approval_gate:not_triggered, rate_budget:absent]
  detection_method: finance-reconciliation-anomaly
  time_to_detect: 6h
  containment: partial
severity:
  band: S2
  reversibility: partial
  blast_radius: many-customers
  detection_lag: hours
  regulatory_trigger: reportable
  loss_confidence: measured
dependency:
  shared_model_family: gpt-x
  shared_framework: langgraph
  correlation_tags: [gpt-x, langgraph, refund-tool-pattern]
provenance:
  source_type: REDTEAM_SYNTHETIC
  contributor_id: anon_4471
  schema_version: ALR-0.1
  redaction_tier: T1_FINGERPRINT
```

## 8. Sellable-now vs poolable-later

| Object | Fill today from… | Becomes valuable when… |
|---|---|---|
| Exposure | a deployment review | always observable |
| Controls | a red-team / audit | corpus shows which controls cut loss |
| Loss Event | red-team findings + public incidents | real claims flow in |
| Severity band | modeled from tests | `measured` from paid claims |
| Dependency | observable now | reinsurer has cross-carrier view |
| Integrity envelope | sealable now | courts/carriers require trusted records |

## 9. Governance & neutrality

- **Open core, versioned:** the schema + the T2 benchmark are open and free. The populated T1 corpus is the proprietary asset.
- **Neutral by construction:** the steward has no underwriting book, ever. Contributors are pseudonymous.
- **Crosswalk, don't compete:** ALR maps onto AIUC-1 / OWASP-ASI / MITRE-ATLAS / OTel-GenAI rather than replacing them.
- **Versioned** (`ALR-0.x`) with a public changelog.

## 10. Open questions (v0 leaves these to humans)

1. Frequency normalization: events per decision, per agent-year, or per $ at risk?
2. Do controls actually correlate with lower loss the way the catalog assumes?
3. Will carriers accept `modeled` synthetic severity as a starting prior, or only `measured`?
4. Is `T1_FINGERPRINT` genuinely shareable and still signal-rich after redaction?
5. Grade scale (`AL1–AL9`) calibration — needs a real distribution.

*ALR-0.1 — a design draft. The schema is the product; the corpus is the asset; neutrality is the moat.*
