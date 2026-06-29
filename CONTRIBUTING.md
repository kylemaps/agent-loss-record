# Contributing to the Agent Loss Record

Thanks for helping build a shared track record of AI-agent failures. The most
valuable contribution right now is a **publicly documented incident** structured
into the ALR schema.

## How to contribute an incident

1. **Open an issue or a pull request.** For a quick suggestion or a source you
   think we should cover, open an issue. To add the record yourself, open a pull
   request.
2. **One record per file.** Add a single incident per file under the appropriate
   directory (e.g. `benchmark/`), structured into the ALR schema in `schema/`.
3. **Cite your sources.** Every record must link to public, verifiable reporting
   (news article, post-mortem, regulator filing, court document, vendor
   disclosure). Include a URL for each claim that needs support.
4. **Public incidents only — for now.** Only submit failures that have already
   been publicly reported. Do not submit confidential, private, or proprietary
   incident data, and do not include personal data or anything under NDA.

## Review

Maintainers will review for schema conformance, source quality, and that the
incident is genuinely public. Expect questions — the goal is a corpus others can
trust and reuse.
