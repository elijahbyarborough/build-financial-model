### Sign Convention (CRITICAL)
On the PP&E Build tab, use the **asset perspective** for all line items:
- **Capital Expenditures**: POSITIVE (adds to asset base)
- **Depreciation / Depletion / Amortization**: NEGATIVE (reduces asset base)
- **Acquisitions**: POSITIVE (adds to asset base) — pulled from Capital Allocation Build
- **Asset Disposals**: POSITIVE or NEGATIVE depending on direction

The CFS tab negates capex when pulling it: `CFS!Capex = -'PP&E Build'!Capex` to show it as a cash outflow. The PP&E Build itself always uses the asset perspective.
