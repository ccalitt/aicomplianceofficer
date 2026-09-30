# MVP scope (working draft)

_Constraint: nobody is full-time. Scope everything to what one or two part-time builders can ship in 6–8 weeks of evenings and weekends._

## 1. The sequence

| Step | What | Why this order | Rough effort |
|---|---|---|---|
| **v0: ₹25k Ceiling Impact Checker** (free, self-serve) | Upload a payroll file, get the affected cohort (₹15,001–25,000 wage band), per-employee old vs new EPF/EPS/EDLI, employer cost delta, and the 50% wage add-back check. No login, or email only | A live reason to act right now (Oct 2026 payroll, ECR due 15 Nov). Doubles as the outreach tool for consultants. Every upload teaches ingestion and seeds the golden set (with consent) | 2–3 weeks |
| **v1: Monthly EPF + ESIC workbench** (Karnataka + Telangana) | Multi-client console. Monthly upload → mapping (confirm once) → engine → validators → ECR text file + ESIC upload file + exceptions + evidence PDF | The core recurring job, monthly and per employee, and the one consultants hate most | 4–6 weeks after v0 |
| v1.1 | PT (KA, TG) + registers (Forms I/IV/IX) generated from the same canonical data | Near-free once canonical data exists; raises value per client | 2–3 weeks |
| v1.2 | Due-date calendar, reminders, client-approval link (client sees the summary and approves) | Retention and workflow lock-in | 2 weeks |
| Later | LWF, gratuity/bonus calculators, annual return, contractor verifier | Per the report's phase plan | — |

**Deliberately out of v0/v1:** portal automation or RPA, credential storage, the POSH/annual return, gig workers, EHS, multi-state beyond KA/TG, payroll-vendor API integrations (file exports are enough).

## 2. v0: Ceiling Impact Checker (detail)

**Flow**
1. Land on the page: "EPF wage ceiling is now ₹25,000. See what it costs you in 60 seconds."
2. Upload an xlsx/csv, or download our template.
3. The AI maps columns (employee ID, name, basic, DA, allowances…) and shows the mapping. The user fixes anything wrong.
4. The user classifies salary components once (wage / excluded / ambiguous). We pre-fill this with the AI and flag ambiguous ones.
5. Results:
   - headcount affected;
   - employer monthly and annual cost delta;
   - employee take-home delta;
   - employees failing the 50% wage test and the add-back amount;
   - a downloadable per-employee sheet.
6. CTA: "Run your monthly ECR on this — join the consultant beta."

**Why it's a good first build**
- It exercises exactly the hard parts: ingestion, component classification and the deterministic EPF engine.
- It is useful even when wrong at the edges (it's a *simulation*, not a filing), so liability is low.
- Consultants can run it for every client and send it on as a value-add. That is our distribution.

**Open items to verify before launch**
- Exact EPS treatment under the new ceiling: does the EPS wage cap move to ₹25k too? What happens to the 8.33% split and to the employer's balance to EPF?
- EDLI and admin-charge bases under the new ceiling.
- Treatment of employees who opted out with Form 11 under the old ceiling and now fall under the new one.
- Rounding rules per the revamped ECR.

## 3. v1: Monthly workbench (detail)

**Core objects**
- `Firm` (consultant practice), `User`
- `Client` (employer): establishment codes (EPF, ESIC), state(s), headcount band
- `ClientFormat`: the confirmed column mapping + component classification, versioned
- `PayrollRun`: client × wage month; uploaded file(s), status
- `EmployeeMonth` (canonical): identifiers (emp id, UAN, IP number), days worked/LOP, gross, components[], state of work, DOB, DOJ/DOE
- `Computation`: engine version, rule-graph version, per-employee results, input hash
- `Exception`: severity, type, message, suggested fix, resolved_by
- `Artifact`: ECR file, ESIC file, evidence PDF (hash recorded in the ledger)
- `LedgerEntry`: append-only; includes the portal acknowledgement (TRRN) entered after upload

**Validators (v1 list)**
- UAN missing or duplicate; name/DOB mismatch against the last month
- Age ≥58 still getting an EPS share
- Wage below state minimum wage (warn only in v1)
- Month-over-month headcount or contribution variance above a threshold
- ESIC: eligibility per contribution period (Apr–Sep, Oct–Mar). Anyone covered at the start of a period stays covered even if wages cross ₹21,000 mid-period. *1 Oct 2026 starts a new period, which makes a nice demo case.*
- ECR format checks mirroring the EPFO portal's rules (sequence, field lengths, totals)

**The UX shape**
- Home: client list × this month's status (Not started / Needs review / Ready / Filed) plus days to due date.
- Run view: **diff vs last month first**, then exceptions, then totals, then downloads.
- Every number is clickable: shows the formula, the rule node, the input row and the engine version.

## 4. Build notes (tech: to decide, suggested defaults)

- **Engine:** a pure-function library, heavily unit-tested, with golden cases from gazette examples. Keep it separate from the app so it can be versioned and shadow-run.
- **Rules graph v0:** YAML/JSON in git (reviewable diffs, effective dates) before any graph DB. Git history *is* the version history at this stage.
- **LLM use:** column mapping and component classification with structured outputs, plus confidence and abstention. PII masked in prompts (send headers and sample shapes, not Aadhaar/bank numbers).
- **Eval harness from day one:** a folder of anonymised payroll files + expected canonical output + expected ECR. CI fails if the confident-wrong rate rises.
- **Hosting:** India region; no credential storage; delete uploaded files after N days in v0.
