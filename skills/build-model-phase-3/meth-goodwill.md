## Goodwill & Intangibles

**Always model amortization schedules.**

### Goodwill
- Goodwill does not amortize under GAAP — carry forward at the most recent balance
- Only adjust for impairments (if modeling a scenario). In projections, acquisition spend flows through the M&A Assets line (see below), not Goodwill — Goodwill stays flat unless the analyst explicitly models a goodwill allocation.

### Intangible Assets
- Build an amortization schedule: Beginning Balance - Amortization Expense + Additions = Ending Balance
- Amortization expense: straight-line over estimated useful life by asset category
- If the company discloses an amortization schedule in filings, use it directly
- Amortization flows to the IS (either as a separate line or within D&A, depending on company reporting)

### M&A Assets (Required BS Row)
Every Balance Sheet must include an "M&A Assets" row in Noncurrent Assets, positioned after Other Noncurrent Assets and before Total Assets.

- **Historical periods**: 0 (or link to reported goodwill from acquisitions if available)
- **Projection periods**: Pull directly from the Capital Allocation Build's **Cumulative M&A Invested Capital** row:
  - `='Capital Allocation Build'!Cumulative_M&A_Invested_Capital` for each projection year
  - This is the running total of all acquisition spend (Phase 4, Section 3, Row 3)
- This line offsets the cash outflow from acquisitions on the CF/BS so the balance sheet stays balanced without touching Goodwill or the organic model
- **Total Assets SUM range must include this row**
- The Phase 4 Re-Link step explicitly maps: `BS!M&A Assets = Cap Alloc!Cumulative M&A Invested Capital`

---
