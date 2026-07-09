## Share Count

**Share count logic lives on the Capital Allocation Build tab**, since buybacks are the primary driver and buyback dollars come from the capital allocation waterfall.

### Diluted Shares via Treasury Stock Method
1. **Basic shares**: Beginning shares - buyback shares + SBC dilution = ending basic shares
2. **Diluted shares**: Basic shares + dilutive impact of options/RSUs via TSM
   - TSM: `Dilutive Shares = In-the-Money Options × (1 - Exercise Price / Current Stock Price)`
   - Current stock price: use market price for historical
   - In the model, the Dilutive Securities row is a flat blue/yellow ASSUMPTION (the modeling default) — TSM is how you SIZE and refresh that assumption: compute TSM at the current price, hardcode the result with a source comment, and refresh when the share price moves materially
3. **Buyback shares**: `Buyback Dollars / Buyback Price` (Buyback Price is a hardcoded assumption on the Capital Allocation Build, separate from the investor's Entry Price on the Returns tab)
4. **SBC Dilution**: `SBC Dollars / Buyback Price` — net share dilution from stock-based compensation at the assumed share price
5. **Diluted share count feeds back to IS** for EPS computation
