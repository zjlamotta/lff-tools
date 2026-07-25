---
name: refresh-market
description: Refresh market data for a specific target market. Searches for latest reports, downloads what's available, and updates wiki articles.
---

# Refresh Market Data

Search for and compile the latest market data for a specific target market.

## Arguments
- `$ARGUMENTS` — market name (e.g., "Columbus", "Tampa", "Nashville", "Kansas City")

## Process

1. **Search for latest reports:** Use WebSearch to find:
   - Lee & Associates quarterly report (often has direct PDF links)
   - Colliers quarterly report
   - Cushman & Wakefield MarketBeat
   - CBRE Figures
   - JLL Market Dynamics
   - Any local brokers with published reports
   - Focus on flex/small-bay specific data where available

2. **Download what's accessible:** Use WebFetch for direct PDF links. Save to `real-estate/raw/{Market}/` with descriptive filenames. Extract with pdftotext.

3. **Extract key metrics:** For each report found, pull:
   - Vacancy (overall + flex/small-bay if segmented)
   - Net absorption (quarterly + annual)
   - Asking rent (NNN, by type if available)
   - Construction pipeline
   - Sale price $/SF and cap rates
   - Notable transactions

4. **Update wiki articles:**
   - `real-estate/wiki/target-market-analysis.md` — update the market's row
   - `real-estate/wiki/midwest-northeast-market-deep-dive.md` or `southeast-market-deep-dive.md` — update the market's section
   - `real-estate/wiki/market-reports-and-deal-pipeline.md` — add new report entries
   - `real-estate/wiki/small-bay-data-vault.md` — update flex vacancy if new data found

5. **Report:** Summarize what was found, what was updated, and any data gaps remaining.
