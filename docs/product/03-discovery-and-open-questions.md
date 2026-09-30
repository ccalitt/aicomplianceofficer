# Discovery plan and open questions

## 1. Riskiest assumptions (test these before building much)

| # | Assumption | How to test cheaply | Kill / pivot signal |
|---|---|---|---|
| A1 | PF/ESI consultants spend meaningful time per client-month on data wrangling and computation, and would pay to cut it | 10–15 consultant interviews (Hyderabad + Bengaluru); ask them to show us last month's workflow | They say it takes 10 min per client and they're fine with it |
| A2 | Client payroll arrives in messy, varied formats (so AI ingestion is the wedge) | Collect 20+ anonymised sample files from interviewees | Most clients are already on greytHR/Keka with clean ECR export |
| A3 | Consultants will upload client payroll data to a third-party tool | Ask directly; probe DPDP and client-confidentiality worries | Hard no, even with India hosting and a DPA |
| A4 | Willingness to pay about ₹100–300 per client-month (or a per-firm plan) | Price-anchoring questions; a pre-sale of the beta at a founding price | They anchor only at the ₹500-per-return they *charge*, not what they'd *pay* |
| A5 | The ₹25k ceiling change is causing real confusion right now | Count errors or questions in consultant WhatsApp groups and forums; interview answers | Everyone's payroll software handled it automatically |
| A6 | Consultants won't see us as a threat that disintermediates them | Ask how they'd feel if the tool were sold direct to employers | Strong fear; then commit to a partner-only channel |

## 2. Consultant interview script (30 min)

1. Tell me about your practice: how many clients, what sizes, what states, which statutory work?
2. Walk me through last month's PF/ESI for one client, step by step. *(Screen-share if possible.)*
3. In what form does client payroll reach you? How many different formats do you deal with?
4. Where do errors happen? Tell me about the last time something went wrong (a 7A/14B notice, a mismatch, a rejected ECR).
5. How did you handle the ₹25k ceiling change? What was confusing?
6. What tools do you use today? What do you pay? What do you charge clients?
7. If a tool gave you a validated ECR + ESIC file + an evidence pack from the raw Excel in 5 minutes, what would that be worth to you?
8. What would stop you from uploading client data to it?
9. Can you share an anonymised sample file? Can we run your next month in shadow for free?

## 3. Outreach starting points (no plan yet: options)

- **Warm:** Sambit's HR/comp network, which can refer the consultants their companies use. Lalit's Darwinbox-era contacts in HR ops.
- **Consultant communities:** PF/ESI consultant WhatsApp/Telegram groups; ICAI branch study circles (Hyderabad, Bengaluru); IndiaMart listings of PF consultants (they're publicly listed, so cold outreach is possible).
- **Content:** a free ceiling checker (v0) + a short explainer post on the ₹25k ceiling and the 50% wage rule, shared on LinkedIn and in HR groups.
- **Associations (later):** FTCCI, TIF-MSME, HYSEA (Telangana); KASSIA, FKCCI, Peenya (Karnataka).

**Suggested first milestone (no one full-time):** 10 interviews + 20 sample files + 3 consultants agreeing to shadow-run November payroll, before writing the v1 workbench.

## 4. Open product questions

**Positioning and business**
- Consultant-only channel forever, or direct-to-employer later? (Channel conflict vs TAM.)
- Pricing unit: per firm seat, per client-month, or per employee? Per client-month is the simplest mental model for consultants.
- Do we ever take on filing accountability (the operator layer), or stay pure software with a partner network of reviewers?

**Product**
- v0 scope: EPF only, or EPF + ESIC? (ESIC adds the contribution-period logic but broadens appeal.)
- Do we need a login for v0? An email gate gives us leads, but friction lowers usage.
- How do we capture the portal acknowledgement (TRRN) back? Manual entry, upload a challan PDF and OCR it, or a browser extension later?
- Can a browser extension that fills EPFO/ESIC portal forms under the user's own session be worth it early, or is it a support nightmare?

**AI / eval (Lalit's area)**
- Golden-set design: which edge cases matter most? Mid-month joiners, arrears, ceiling crossing, age 58, international workers, LOP, multi-state PT.
- Confidence calibration for column mapping: per column or per file? What threshold routes to human confirmation?
- Self-hosted open-weight model for PII-bearing extraction vs a hosted API with PII masking?
- How do we measure "confident-wrong" when ground truth only arrives after a portal rejection or a notice?

**Regulatory facts to verify** (before any customer-facing output)
- EPS/EDLI/admin-charge treatment under the ₹25k ceiling.
- Karnataka and Telangana state-rule status (the sources conflict).
- KA/TG PT slabs and due dates; LWF amounts.
- ESIC wage ceiling still ₹21,000 under the Code?
