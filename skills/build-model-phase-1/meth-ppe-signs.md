### Sign Convention (CRITICAL)
In the PP&E & Capex section of the BS & CFS Build, use the **asset perspective** for all line items:
- **Capital Expenditures**: POSITIVE (adds to asset base)
- **Depreciation / Depletion / Amortization**: NEGATIVE (reduces asset base)
- **Acquisitions**: POSITIVE (adds to asset base) — pulled from Capital Allocation Build
- **Asset Disposals**: POSITIVE or NEGATIVE depending on direction

The CFS tab negates capex when pulling it: `CFS!Capex = -'BS & CFS Build'!Capex` to show it as a cash outflow. The PP&E & Capex section itself always uses the asset perspective.
