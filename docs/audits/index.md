# Audit Reports

Public audit reports produced by the Trilocore stack:
**[BEVM](../bevm.md)** (find + prove) → **[Janus](../janus.md)** (reason + write).

Each report is the *self-published* output of an autonomous run: Janus's
LangGraph *Propose → Locate → Exploit → Verify* loop concludes, the structured
findings are extracted under an [Instructor](https://github.com/jxnl/instructor)
schema, and the report is committed straight into this docs site. No
copy-pasting, no manual reformatting.

!!! info "How a report gets here"
    1. **BEVM** loads a target contract on a mainnet fork and runs detectors.
    2. **Janus** drives the *Propose → Locate → Exploit → Verify* loop until an
       exploit is proven (or the contract is shown inert).
    3. The agent calls `WriteDocumentTool` — a structured pydantic
       `AuditReport` is rendered to Markdown with MkDocs front-matter and
       committed into [`trilocore/trilocore-docs`](https://github.com/trilocore/trilocore-docs)
       under `docs/audits/`.
    4. GitHub Pages picks up the push and the report goes live at
       `docs.trilocore.ai/audits/...`.

## Index

See the navigation sidebar for individual reports. Reports are listed
**newest first**; each filename is `YYYY-MM-DD-<short-slug>.md`.

## Severity

| Tag | Meaning |
|-----|---------|
| **CRITICAL** | Funds drainable by an unprivileged caller; demonstrated by a successful fork replay. |
| **HIGH** | Funds drainable under a constrained but realistic precondition (privileged caller, single-block flash-loan, oracle lag). |
| **MEDIUM** | Logic flaw or gate bypass with no direct profit path on its own — risky when composed. |
| **LOW / INFO** | Code-quality, defense-in-depth, or hardening notes — no immediate exploit. |

Every CRITICAL/HIGH report carries a **fork-replay verdict** with the attacker's
`profit_wei` measured against a baseline snapshot — i.e. the exploit was
*proven*, not just hypothesized.

---

!!! note "Disclosure"
    Reports may be published before all referenced contracts have been
    upgraded or paused. Where this is the case it is called out at the top of
    the report. To report a finding privately, email
    [security@trilocore.com](mailto:security@trilocore.com) — see the
    [responsible disclosure](https://trilocore.com/.well-known/security.txt)
    policy on the main site.
