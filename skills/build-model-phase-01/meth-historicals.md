<!-- CANONICAL COPY: build-model-phase-01/meth-historicals.md. If editing, sync edits there first. -->

## Historicals Capture Methodology (Annual Historicals & Quarterly Historicals)

The **Annual Historicals** and **Quarterly Historicals** tabs are the model's capture layer: the single place company-reported data is entered, and the single place it gets updated after every earnings release and filing. Everything else in the model — IS/BS/CFS historicals, build tab historicals, the KPI Tracker — links here via formulas.

### The Two Governing Rules

**1. Single Hardcode Layer.** These two tabs are the ONLY place in the model where hardcoded historical values are allowed. Every captured cell: blue text (#0000FF) + source comment in the format `Source: [Document Type], [Date], [Specific Reference]` (e.g., `Source: URI 10-Q, Q3 2026, Segment Revenue Table p.14`). No model tab ever links to a raw source tab (BAMSEC/Tegus/broker) — data leaves a source only by being captured here. The audit trail is the source comment, one click away.

**2. Capture Everything.** Enter every line the company reports in each section — full statement faces, every segment line, every disclosed KPI. Do not summarize, consolidate, or skip lines at capture; summarization happens downstream (statements, Model Tab, KPI Tracker), never here. If it will be hard to drive a working capital item, a BS investment line, or a segment later, it is because the piece wasn't captured — depth of capture is what makes the build tabs possible. When unsure whether something is worth capturing: capture it.

### Column Conventions

- **Annual Historicals**: one column per fiscal year, `FY YYYYA` labels, all available history (no fixed minimum — use what the data gives you).
- **Quarterly Historicals**: one column per fiscal quarter, `FQN YYYYA` labels, **at least 16 quarters** of history; a new column is appended each quarter post-earnings.
- Standard Tab Header (rows 1-6) per firm-formatting; units declared once in row 2 (e.g., `($ in millions)`); enter all values in the declared units.
- Formulas ARE allowed on these tabs for **derived values only** (Q4 derivation, YTD-to-discrete conversion, tie-out checks) — black text per color coding, since they are calculations, not captured inputs.

### Canonical Section Hierarchy

Section order follows the analyst's update workflow: **press-release data first** (available the day of earnings), **filing data second** (10-Q/10-K, days later), provenance last. Both tabs share the same hierarchy. **Sections the company does not report are omitted entirely — never left as empty headers.**

| # | Section | Typical contents (capture as reported) |
|---|---------|----------------------------------------|
| 1 | **Income Statement** | Full IS face: every revenue line, cost line, subtotal, per-share figure as presented |
| 2 | **Segment Data** | Revenue and segment profit (operating income / EBITDA / gross profit — whatever measure the company uses) by reportable segment; segment D&A, capex if disclosed. Versioned blocks — see Segment Recast protocol |
| 3 | **Geographic Data** | Revenue (and profit if disclosed) by region/country as presented |
| 4 | **Operational / KPI Data** | Sector-driven, per the Phase 0 taxonomy: volumes, ASP, subscribers/net adds, ARR/NRR, stores, same-store sales, backlog/RPO, bookings/book-to-bill, DAU/MAU, AUM & flows, NIM, combined ratio, RevPAR, occupancy, utilization, headcount — everything the company discloses each period |
| 5 | **Share Count & Per-Share** | Weighted average basic and diluted shares, end-of-period shares outstanding, DPS declared, buyback dollars and shares repurchased, SBC expense |
| 6 | **Guidance** | Management guidance as given each period (revenue, EPS, margin, capex ranges — low/mid/high), later joined by the corresponding actual. Enables guide-vs-actual analysis on the KPI Tracker. Primarily a Quarterly Historicals section |
| 7 | **Balance Sheet** | Full BS face as reported — quarterly too (capture-everything; quarterly BS drives net debt trend, WC seasonality) |
| 8 | **Cash Flow Statement** | Annual: full face. Quarterly: as-reported **YTD** figures PLUS derived **discrete quarters** (`Qn = YTDn - YTDn-1`, black formulas) — see Quarterly CFS Mechanics |
| 9 | **Debt Detail** | Footnote detail: tranches, coupons/rates, maturities, revolver capacity and availability, covenants if disclosed |
| 10 | **Lease Detail** | ASC 842 footnote data: ROU assets and liabilities by class, lease cost components, maturity schedule, weighted-average term and discount rate. Feeds the lease flags and Lease BS Map |
| 11 | **Non-GAAP Reconciliations** | The company's own reconciliation tables (GAAP → adjusted EBITDA/EPS/FCF) exactly as reported |
| 12 | **Miscellaneous** | Recurring disclosures that fit nowhere above: D&A by segment, pension, FX impacts, restructuring detail, etc. |
| 13 | **Change Log** | Bottom of tab, append-only — see below |

Formatting: each section is a Tier 1 header (navy #1C3553); sub-blocks within a section (e.g., individual segments) are Tier 2 subheaders (#C2D5EB). All other firm-formatting rules apply (number formats with `_)` padding, spacer rows, italics on % rows).

### Quarterly Mechanics

- **Q4 derivation**: Where the company does not report Q4 discretely (most US filers: 10-K only), derive it: `Q4 = FY - (Q1 + Q2 + Q3)`. Black formula referencing the Annual Historicals FY column and the three captured quarters — NOT a hardcode.
- **Quarterly CFS (YTD → discrete)**: 10-Qs report CFS on a YTD basis. Capture the YTD figures as reported (blue hardcodes), then add a derived discrete-quarter block: `Q1 discrete = Q1 YTD; Qn discrete = YTDn - YTDn-1` (black formulas). Downstream consumers (KPI Tracker) use the discrete block.
- **ΣQ = FY tie-outs**: For every flow-statement section captured quarterly (IS, discrete CFS, segment revenue/profit), include a tie-out row per key line: `Sum of Q1-Q4 - FY = 0`. Non-zero → flag and resolve (usually a restatement, a derived-Q4 error, or a units mismatch). Balance-sheet items are point-in-time — Q4 BS must equal FY BS exactly; check that too.

### Edge-Case Protocols

**Segment reporting change (recast).** Companies periodically change reportable segments and recast prior periods. NEVER overwrite the old presentation in place:
1. Freeze the existing segment block; relabel its Tier 2 header "(pre-FYxxxx presentation)".
2. Build a new versioned segment block below the frozen one, from the company's recast disclosures (recast 8-K, restated 10-K segment footnote).
3. Backfill the new block only as far back as the company recast. Where older periods were not recast, leave them blank in the new block and add a note row documenting the seam (e.g., "Recast data available FY2022 forward").
4. **Model links point ONLY at the current-presentation block.** Re-point any statement/build/KPI links from the frozen block to the new one.
5. Change Log entry + update the `SEGMENT_PRESENTATION` flag in the Task Tracker Model State Block (e.g., "FY2025 presentation").

**Accounting standard adoption (ASC 606, 842, etc.).** Add a note row in the affected section marking the adoption period and method (modified retrospective vs full retrospective). NEVER self-restate pre-adoption periods — capture only what the company reported. The comparability break is documented, not repaired. Change Log entry (e.g., "ASC 842 adopted FY2019, modified retrospective; prior years not restated").

**Fiscal year-end change.** The transition creates a stub period. Add an explicit stub column labeled `FY YYYYT (Xmo)` (e.g., `FY 2024T (6mo)`) between the old- and new-basis years. Growth rates and CAGRs must skip or annualize the stub — never treat it as a full year. Quarterly labels re-key to the new fiscal calendar from the change forward; document the mapping in the Change Log.

**Restatement (10-K/A, 10-Q/A, error correction).** Overwrite the affected cells with restated values; update each cell's source comment to cite the amending filing; Change Log entry listing periods affected.

**Discontinued operations reclass.** When a company moves a business to discontinued operations, prior periods are recast to continuing-ops. Treat like a restatement: overwrite with the company's recast continuing-ops figures, comment cites the recasting filing, Change Log entry. Capture the disc-ops line itself as reported.

**52/53-week fiscal years (retail).** Add a "Weeks in Period" row (52 or 53 / 13 or 14) at the top of Section 1 for affected companies. Flag 53-week years in growth comparisons — a note row suffices.

### Change Log (Section 13)

Append-only table at the bottom of each Historicals tab:

| Date Entered | Change Type | Periods Affected | Description | Source |
|---|---|---|---|---|
| 03/04/26 | Segment recast | FY2022-FY2025 | New 3-segment presentation; pre-FY2026 block frozen | 8-K recast, 2026-02-15 |

Change types: `Segment recast`, `ASC adoption`, `FYE change`, `Restatement`, `Disc ops reclass`, `Other`. Every edge-case protocol above requires an entry. Routine quarterly data additions do NOT get logged — only changes to previously captured data or presentation.

### Post-Earnings Update Workflow (ongoing maintenance)

After initial build, each reporting period:
1. **Earnings day**: append the new FQ column on Quarterly Historicals; capture sections 1-6 from the press release; update Guidance with the new guide.
2. **Filing day (10-Q/10-K)**: capture sections 7-12; if a 10-K, also append the new FY column on Annual Historicals (full capture) and derive Q4.
3. Run the ΣQ=FY tie-outs and Q4-BS=FY-BS check.
4. Apply any edge-case protocol triggered by the filing (recast, restatement, etc.).
5. Downstream tabs (statements, KPI Tracker) update through their existing links; extend link ranges where a new column was added.
