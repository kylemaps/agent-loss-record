# Agent Loss Record (ALR)

**An open standard for recording AI-agent failures — the missing data layer for pricing AI risk.**

## What this is
When an AI agent fails — leaks data, takes a wrong action, gives harmful advice — it causes real, measurable harm. But there's no common way to record those failures and no shared history of them. Insurers now writing "AI cover" openly admit they can't price it, because the loss data doesn't exist.

The **Agent Loss Record (ALR)** is an open, structured format for recording an AI-agent failure the way insurance records any other risk: exposure, controls, the event, severity, and dependencies. Think of it as the start of a shared track record for AI failures — a data layer the whole field can build on.

## What's here
- `schema/` — the ALR schema (the record format).
- `benchmark/` — a seed corpus of real, publicly documented AI-agent failures structured into the schema, plus the aggregate AI-Agent Loss Benchmark.

## Honest status (v0)
Early, open draft. The seed corpus is built from **public incidents** and is **modeled, not measured** — public reporting gives the shape of a failure (what went wrong, what control was missing, rough severity) but not the verified dollar loss or exposure base. Those come only with real claims data. v0 proves the structure and surfaces patterns; it is not a pricing tool.

## Contributing
Contributions of public incidents structured into the ALR schema are welcome — open an issue or a pull request. See CONTRIBUTING.md.

## License
Apache-2.0. Benchmark data may also be reused under CC-BY-4.0.

## Maintainer
Kyle Mapue 
