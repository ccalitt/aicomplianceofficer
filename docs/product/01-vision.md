# Product vision (working draft)

_Owner: Lalit · Status: brainstorm · Last updated: 30 Sep 2026_
_Source research: "AI-Native Statutory Compliance for Indian Employers" (Compass report, 30 Sep 2026)_

## 1. What we believe

1. **Compliance work in India is paid-for labour.** Consultants, CAs and branch staff turn messy payroll into correct statutory filings. Aparajitha earns about ₹12–13 lakh of revenue per employee, which shows it is a labour business.
2. **The Labour Codes shrink the paper but make the logic harder.** Registers fall from 84 to 8, and 31 returns become 1. But the 50% wage definition, the ₹25k EPF ceiling (from 17 Sep 2026), fixed-term gratuity and 36 states moving at different speeds all make the logic harder. Harder logic plus less paper favours software over branch staff.
3. **"AI-enabled" trackers are not the answer.** Komrisk and Simpliance list obligations and leave the work to the client. Payroll SaaS (greytHR, Keka, Zoho) computes contributions but does not own applicability, rule versioning or evidence.
4. **An LLM is not the moat.** The moat is four things together:
   - a versioned rules graph;
   - a deterministic engine;
   - an eval harness that measures the *confident-wrong rate*;
   - an evidence ledger.

## 2. One-line vision

> **The AI back office for statutory payroll compliance.** You give it payroll data in any shape. It returns correct, portal-ready filings with the rule citations and evidence to defend them, and it tells you when it isn't sure.

## 3. Who it is for (in order)

| # | User | Why them first | What they'd pay for |
|---|---|---|---|
| 1 | **PF/ESI consultants and small CA practices** (20–150 client payrolls each) | They already do this work every month in Excel. One signup brings many entities. They hold the DSC relationship and the liability, and they can adopt a tool without an enterprise sale | Hours saved per client-month, fewer errors, a professional evidence pack to show clients |
| 2 | **In-house HR/finance at 20–500-staff firms on Excel/Tally payroll** | Same pain, no consultant | Peace of mind and a readiness score |
| 3 | **Principal employers verifying contractors** (Phase 2) | Highest willingness to pay; liability under s.55 | Invoice gating, vendor scores |

We are **not** building for enterprises with managed compliance teams (TeamLease territory) or for firms whose payroll SaaS already covers them well, until later.

**Why consultants first (the reframe from the report):** the report's "partner-led operator" makes partners resellers of our service. Software-first flips that: **the consultant is the user**, and the product makes one consultant do the work of three. We can add an operator layer later (our reviewers, an accuracy guarantee) as a paid upsell. It is not the base product.

## 4. What "AI-native" means here (and what it doesn't)

**AI does:**
- **Ingestion.** Map any payroll Excel/CSV layout ("Basic+DA", "Spl Allow", "Conv", merged headers, totals rows) into a canonical employee-month record. *This is the single biggest labour sink today and the clearest AI win.*
- **Classification.** Tag salary components as wage / excluded, which drives the 50% add-back. Tag roles for minimum-wage skill categories (later).
- **Anomaly explanation.** "Ramesh's EPF rose ₹1,200 because his basic crossed ₹15,000 and the ceiling is now ₹25,000."
- **Regulatory watch.** Read gazettes and EPFO/ESIC circulars and propose rule-graph diffs for human approval.
- **Conversational queries** over a client's history ("which employees crossed the ESIC ceiling this period?").
- **Drafting.** Notices, employee communications, and replies to inspection notices (later).

**AI never does:**
- **Compute money.** All amounts come from a deterministic, unit-tested engine.
- **Hold credentials or submit to government portals.** The consultant or employer uploads with their own DSC/e-sign.
- **Guess silently.** Every AI output carries a confidence score. Low confidence routes to a human, and an abstention beats a confident wrong answer.

## 5. Product principles

1. **Cite everything.** Every number points to an engine version, a rule node (with `effective_from` and a gazette reference) and the input rows.
2. **Abstain > guess.** Track the abstention rate and the confident-wrong rate as first-class product metrics, not only ML metrics.
3. **Confirm once, reuse forever.** A human confirms a client's column mapping and component classification once. After that, every month runs straight through unless something changes.
4. **Diff-first UX.** Consultants care about *what changed since last month*, so lead with variance, not totals.
5. **Stay payroll-agnostic.** Accept Excel, CSV, and greytHR/Keka/Zoho/Tally exports. Never require a client to switch payroll.
6. **Evidence is a product, not a log.** The inspection/evidence pack is something the consultant hands to their client, so make it look good.
7. **Earn autonomy.** Each obligation × state climbs the autonomy ladder (shadow → assisted → sampled → auto) only on measured eval results.

## 6. System sketch

```
 Payroll files (xlsx/csv/exports)
          │
          ▼
 ┌──────────────────┐   low confidence   ┌───────────────┐
 │ Ingestion agent  │ ─────────────────► │ Human confirm │ (once per format)
 │ (LLM mapping +   │ ◄───────────────── │  mapping UI   │
 │  schema checks)  │                    └───────────────┘
 └────────┬─────────┘
          ▼
 Canonical employee-month  ──►  Rules graph (versioned: state × code × effective date)
          │                              │
          ▼                              ▼
 ┌──────────────────────────────────────────────┐
 │ Deterministic engine (pure functions, tests) │  EPF/EPS/EDLI, ESIC, PT, 50% wage …
 └────────┬─────────────────────────────────────┘
          ▼
 Validators (portal-check replicas) + anomaly detection (MoM variance, UAN/KYC gaps)
          │
          ▼
 Outputs: ECR file · ESIC file · PT return · exceptions · evidence pack (hash-chained ledger)
          │
          ▼
 Consultant reviews → uploads to portal with client DSC → records TRRN/ack back in ledger

 Cross-cutting: Eval harness (golden sets, confident-wrong gate on every release)
```

## 7. Moat over time

| Horizon | Moat |
|---|---|
| Months 0–6 | Speed: the ₹25k ceiling and the Labour Code transition give us a live reason to switch. Ingestion that handles ugly real-world Excel |
| Months 6–18 | Golden sets and the eval harness built from real client-months. A multi-state rules graph with confidence flags. Consultant workflow lock-in (history, evidence) |
| 18+ months | Data network effects: benchmark anomalies across thousands of payrolls. Contractor verification network (the same vendors appear across many principal employers) |
