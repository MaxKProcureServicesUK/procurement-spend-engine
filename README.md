# Procurement Spend Intelligence Engine — v0.4

**Built by PSL · Analyst-operated · Not self-serve**

A structured spend analysis tool for procurement practitioners. Takes raw invoice line data and produces a verified, heuristic-driven analysis with a full audit trail, data quality signals, and a sequenced action plan. Designed to support expert judgement, not replace it.

---

## What it does

Accepts an ERP/P2P invoice export and runs six independent verification checks before producing any analysis. Once verified, it applies six embedded procurement heuristics to identify commercial opportunities — with explicit separation of evidence, assumptions, and validation requirements for each finding.

Outputs include:

- Verified spend overview (totals, supplier counts, invoice lines)
- Six heuristic analyses with confidence levels and effort ratings
- A full logistics audit with false-positive classification and audit trail
- Data quality signals mapped to procurement maturity indicators
- A sequenced action plan with owners, horizons, and saving estimates
- An executive five-slide narrative and email-ready summary
- A spend cube (Supplier × Category × Time)

---

## Who it is for

PSL analysts running initial spend analysis for procurement clients. It is not a self-serve product. Every output that could mislead — price variance, embedded logistics, unclassified spend — is either disabled or flagged for analyst review before client presentation.

---

## Current version: v0.4

Tested against a £59.2M dataset — 18,703 invoice lines, 732 suppliers, four geographic regions.

### What's in v0.4

- Six verification checks with independent re-run formulas
- Six heuristics covering: site-by-site behaviour, year-on-year trend, supplier consolidation, price variance, order batching, logistics efficiency
- Price variance (H4) auto-disables when unit price data is corrupted — protects against false conclusions in supplier conversations
- Logistics keyword audit with false-positive detection (milestone payment terms excluded from freight figures)
- Data quality section mapped to procurement maturity signals
- Full action plan with saving estimates, owners, horizons, and data requirements
- Executive narrative (five slides + email summary)
- Spend cube with category and supplier breakdown

### Known limitations in v0.4

- No site/cost centre dimension — site-by-site heuristic (H1) cannot be completed without ERP cost centre extract
- No prior year data — year-on-year analysis is directional only
- Price variance (H4) is disabled if unit prices contain errors below £0.01 — requires AP data correction before re-enabling
- Category classification relies on client's P2P system — 61% unclassified spend is common in first-pass datasets
- Saving estimates are heuristic-based, not audited — must not be reported as confirmed savings without validation

---

## How to run

1. Export invoice line data from ERP/P2P in standard format (one row per line, columns: SupplierName, SupplierReference, Description, Sum of Gross, Sum of Vat, Sum of Quantity, Date)
2. Open the tool and load the dataset
3. Six verification checks run automatically — review before proceeding
4. Work through heuristics in sequence — read the evidence, assumptions, and validation requirements for each before drawing conclusions
5. Complete the logistics audit manually — confirm false-positive exclusions
6. Review data quality signals — address blockers (unit prices, cost centres) before commercial conversations
7. Generate executive output — adapt client name, year, and date fields

---

## Verification checks

Six checks run against source data before any analysis. All must pass.

| Check | Method |
|-------|--------|
| Total gross spend | Pivot sum — exact match required |
| Invoice line count | Row count — exact match required |
| Unique supplier count | DISTINCTCOUNT on SupplierName |
| Zero-VAT share | SUMIF where VAT=0 ÷ total gross |
| Top 10 supplier share | Sort desc, sum top 10 ÷ grand total |
| Embedded logistics spend | Keyword match: delivery / carriage / freight / transport / shipping |

Re-run formulas are provided inside the tool for independent verification.

---

## The six heuristics

Each heuristic is structured as: trigger → evidence → assumptions → validation required → data needed → indicative saving range.

| # | Heuristic | Typical impact | Typical effort |
|---|-----------|----------------|----------------|
| H1 | Site-by-site spend behaviour | High | 90+ days — needs ERP cost centres |
| H2 | Year-on-year spend trend | Medium | 0–30 days — needs prior year data |
| H3 | Supplier consolidation | High | 90+ days |
| H4 | Same supplier, different prices | High | 30–90 days — **auto-disabled if data corrupted** |
| H5 | Order consolidation and batching | Medium | 0–30 days — fastest implementable action |
| H6 | Delivery and logistics efficiency | Medium | 30–90 days |

H4 disables automatically when unit prices below £0.01 are detected. This is intentional — presenting price variance from corrupted data in client conversations destroys credibility. The heuristic re-enables once data is corrected.

---

## Logistics audit

The logistics section runs a keyword search across invoice descriptions (delivery, carriage, freight, transport, shipping) and then requires analyst review to identify false positives — typically milestone payment terms that contain the word "delivery" as a commercial condition, not a freight charge.

The tool distinguishes:

- **Genuine embedded freight** — delivery charges within non-logistics supplier invoices
- **False positives** — milestone payment terms, receipt acknowledgements, project service bundles
- **Direct logistics category** — IFS Courier and equivalent direct logistics suppliers

The genuine embedded freight figure and the direct logistics category total are kept separate. A combined indicative total is provided but marked as requiring analyst validation.

---

## Data quality signals

Data gaps are treated as procurement maturity signals, not errors to fix before analysis starts. The tool maps each gap to its likely cause and the action required.

Key signals in v0.4:

| Signal | Likely cause | Impact on analysis |
|--------|-------------|-------------------|
| Unclassified spend (61%+) | No category management framework in P2P | Blocks consolidation and benchmarking |
| Unit prices below £0.01 | Quantity entered in value field (AP input error) | Disables H4 price variance |
| Supplier name variants | No master data governance | Understates supplier concentration |
| No site/cost centre | BU spend accountability not enforced | Blocks H1 site analysis |
| High zero-VAT share | Multi-jurisdiction operation | Requires tax review before commercial action |

---

## Saving estimates — required disclaimer

All saving ranges produced by this tool are derived from embedded procurement heuristics calibrated to construction, engineering, and infrastructure sector benchmarks. They are directional hypotheses, not audited projections.

**No saving figure from this tool should be:**
- Reported to stakeholders as a confirmed or committed saving
- Included in a business case without individual validation
- Communicated to a supplier before contract and market data have been reviewed
- Used as a basis for headcount or budget decisions

PSL accepts no liability for decisions taken on the basis of indicative figures without completion of the recommended validation steps.

---

## Data requirements

Minimum to run the tool:

- Invoice line export with: supplier name, supplier reference, description, gross value, VAT value, quantity, date

Significantly improves analysis:

- Site / cost centre / project code per invoice line
- Prior year spend in the same format
- Contract register or preferred supplier list
- Agreed unit prices / rate cards per supplier
- PO vs. non-PO flag per invoice line
- Corrected unit prices (after AP review)
- FX normalisation table for multi-currency datasets
- Supplier legal entity master

---

## Roadmap

- **v0.5** — Automated supplier name deduplication and legal entity matching
- **v0.5** — FX normalisation for multi-currency datasets
- **v0.6** — Prior year comparison (H2 baseline) when second dataset is provided
- **v0.6** — Cost centre / site dimension support (unlocks H1 in full)
- **v1.0** — Category taxonomy auto-classification against UK SIC codes

---

## Prepared by

PSL — Procurement Sustainability Technology Education

This tool is analyst-operated and confidential. It is not licensed for redistribution or self-serve client access without PSL authorisation.
