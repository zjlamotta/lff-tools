---
name: underwrite
description: Produce the standardized one-page data scorecard for an industrial/flex deal from an OM/flyer — extract, screen vs fund criteria, comps, capital stack, price sensitivity & return targets, PM first-call. Presents the numbers; gives no verdict. Use when you want the full underwriting scorecard. For a fast Pass/Borderline/Pursue gut-check first, use `om-screen`; for a full IC memo after committing, use `om-deep-dive`.
---

# Auto-Underwrite

Fast-screen an industrial/flex deal from an OM or flyer. Output a standardized scorecard — no conversational back-and-forth needed for the initial screen.

## Input

One or more files: OM PDF, flyer, rent roll, expenses spreadsheet. Read them all.

## Process

Run Steps 1-5 in sequence. Do NOT stop to ask questions — produce the full scorecard from whatever data is available. Flag missing data as "NOT PROVIDED" rather than asking.

### Step 1: EXTRACT

Read every page of the OM/flyer. Extract into this exact structure:

```
PROPERTY
  Address:
  City, State, Zip:
  Market (match to target market list):
  Property Type (Industrial / Flex / Warehouse):
  
BUILDING
  Total SF:
  Land (acres):
  Year Built:
  Construction:
  Class:
  Stories:
  Clear Height:
  Drive-In Doors:
  Dock Doors:
  Parking:
  Bay Count:
  Bay Sizes:
  Office %:

OCCUPANCY
  Occupied SF:
  Vacant SF:
  Occupancy %:
  Tenant Count:

RENT ROLL (table)
  | Unit | Tenant | SF | Monthly Rent | $/SF/yr | Lease Type | Lease End |

FINANCIALS
  Asking Price:
  $/SF:
  In-Place NOI:
  Going-In Cap:
  Transition NOI (if provided):
  Stabilized NOI (if provided):
  
EXPENSES (table)
  | Category | Annual | $/SF |
  
CAPEX NOTES
  Roof:
  HVAC:
  Other:

BROKER
  Name:
  Firm:
  Phone:
  Email:
```

If a field is not in the OM, write "NOT PROVIDED". Do not guess.

### Step 2: SCREEN

Read the screening criteria file:
`/home/zjlammotta/projects/knowledge-base/real-estate-fund/wiki/deal-screening-criteria.md`

Find the section for the matching market. Run every check and output a table:

```
SCREENING RESULTS
  | Criterion | Threshold | This Deal | Result |
  | Market | Target list | {market} | PASS/FAIL |
  | Building SF | >10K, sweet spot 20-80K | {sf} | PASS/FLAG/FAIL |
  | Year Built | >1970 (or renovated) | {year} | PASS/FAIL |
  | Stories | ≤2 | {stories} | PASS/FAIL |
  | Office % | <40% | {pct} | PASS/FAIL |
  | Condo | No | {yes/no} | PASS/FAIL |
  | $/SF | Green: <${X} | {psf} | GREEN/YELLOW/RED |
  | Going-In Cap | ≥{X}% | {cap} | PASS/FAIL/TBD |
  | In-Place Rent NNN | ≥${X}/SF | {rent} | PASS/FLAG |
  | Deployment Phase | {phase} | — | INFO |
  
  OVERALL: PASS / PASS WITH FLAGS / HARD REJECT
```

If the deal has ANY hard reject (sub-10K SF, pre-1970 unrenovated, >40% office, condo, >2 stories, not in target market), stop here and report HARD REJECT with the reason. Don't run Steps 3-5.

### Step 3: BENCHMARK

Read the comps database:
`/home/zjlammotta/projects/knowledge-base/real-estate/wiki/target-market-comps-database.md`

Read the market analysis:
`/home/zjlammotta/projects/knowledge-base/real-estate/wiki/target-market-analysis.md`

Read flex vacancy data:
`/home/zjlammotta/projects/knowledge-base/real-estate/wiki/small-bay-data-vault.md`

Output:

```
COMP BENCHMARK
  | Metric | Market Avg | This Deal | vs Market |
  | Sale $/SF (Industrial) | ${X} | ${Y} | +/-Z% |
  | Sale $/SF (Flex) | ${X} | ${Y} | +/-Z% |
  | Lease NNN $/SF | ${X} | ${Y} | +/-Z% |
  | OpEx $/SF | ${X} | ${Y} | +/-Z% |
  | Flex Vacancy | {X}% | {Y}% | — |
  | Rent Growth (YoY) | {X}% | — | — |
```

### Step 4: CAPITAL STACK & TOTAL BASIS

Calculate total cost basis using LFF standard assumptions:

```
CAPITAL STACK
  Purchase Price:                    ${X}
  Closing Costs ($5/SF):             ${Y}
  CapEx Reserve ($5/SF):             ${Y}
  TI Reserve ($5/SF):                ${Y}
  Working Capital ($2/SF):           ${Y}
  Leasing Commissions (7% base rent on vacant SF, Year 1): ${Y}
  ─────────────────────────────────────
  Total Cost Basis:                  ${Z}
  Total $/SF:                        ${W}

DEBT (65% LTV on purchase price)
  Loan Amount:                       ${X}
  Rate: SOFR + 350bps (bridge) or fixed (perm) — use current SOFR + 350
  Term: 5 years
  Amortization: 25 years (use 25; note if 30 would apply)
  Annual Debt Service:               ${X}
  Monthly Payment:                   ${X}

EQUITY
  Total Equity Required:             Total Basis - Loan Amount
  Equity $/SF:                       ${X}
```

For leasing commissions: calculate 7% of Year 1 base rent on VACANT units only (occupied units already have leases). If no vacancy, LC = $0.

For SOFR: use 4.30% as default (check if user provides current rate). Bridge rate = SOFR + 3.50% = ~7.80%.

### Step 5: PRICE SENSITIVITY & RETURNS

If asking price is provided, use it as the anchor. If unpriced, use the market flex avg $/SF × total SF as the anchor.

Output three tables:

**Table A: Cap Rates at Different Prices**
```
  | Price | $/SF | Cap (Current NOI) | Cap (Transition) | Cap (Stabilized) |
  | Asking / Anchor | | | | |
  | -10% | | | | |
  | -15% | | | | |
  | -20% | | | | |
  | -25% | | | | |
```

**Table B: Price to Hit Target Caps**
```
  | Target Cap | On Current NOI | On Transition NOI | On Stabilized NOI |
  | 7.0% | ${X} (${Y}/SF) | ${X} (${Y}/SF) | ${X} (${Y}/SF) |
  | 7.5% | | | |
  | 8.0% | | | |
  | 8.5% | | | |
```

**Table C: Levered Returns (at each price point from Table A)**
```
  | Price | Total Basis | Equity | Annual DS | Cash Flow (Stab NOI - DS) | Cash-on-Cash | vs 7% Target |
  | ... | | | | | | |
```

Cash-on-Cash = (Stabilized NOI - Annual Debt Service) / Total Equity

Flag if Cash-on-Cash is below 7% target at any price point.

Note: IRR and equity multiple require hold period modeling beyond this scorecard. State: "Levered IRR target: 20-22%. Equity multiple target: 2.0-2.3x over 5-year hold. Run full model to verify."

### Step 6: SCORECARD OUTPUT

Compile everything into this final output format:

```
═══════════════════════════════════════════════════════════
  DEAL SCORECARD: {Address}
  Market: {City, State} ({Market Name}) — Phase {X}
  Screening: {PASS / PASS WITH FLAGS / HARD REJECT}
═══════════════════════════════════════════════════════════

QUICK NUMBERS
  {SF} SF | {Bays} bays | Built {Year} | {Occupancy}% occupied
  Asking: ${X}M (${Y}/SF) | NOI: ${X} | Cap: {X}%
  
  Market $/SF: ${avg} avg | This deal: ${psf} | {above/below} by {Z}%
  Market NNN: ${avg}/SF | In-place: ${X}/SF | {above/below} by {Z}%
  Flex vacancy: {X}% | Rent growth: +{Y}% YoY

TOTAL BASIS (at asking)
  Purchase: ${X}M | + Closing/Reserves/TI/LC: ${Y} ($15-20/SF)
  Total basis: ${Z}M (${W}/SF all-in)
  Debt (65% LTV): ${X}M at {rate}% | DS: ${X}/yr
  Equity required: ${X}M

SCREENING
  {Table from Step 2}

RETURNS
  Cash-on-Cash (stabilized): {X}% {vs 7% target: PASS/FAIL}
  Unlevered yield (stabilized): {X}%
  IRR/EM: Run full model (target 20-22% IRR, 2.0-2.3x EM, 5yr hold)

PRICE SENSITIVITY
  {Tables A, B, C from Step 5}

KEY POSITIVES
  - {bullet}
  - {bullet}
  - {bullet}

KEY RISKS
  - {bullet}
  - {bullet}
  - {bullet}

OPEN QUESTIONS FOR BROKER
  1. {question}
  2. {question}
  3. {question}

PM FIRST CALL
  {from property-management-vendor-list.md for this market}

BROKER
  {Name} | {Firm} | {Phone}
═══════════════════════════════════════════════════════════
```

## Rules

- Do NOT give a verdict (Pass/Counter/Pursue). The scorecard presents data; Zach makes the call.
- Do NOT ask clarifying questions during the process. Use "NOT PROVIDED" for missing data.
- Do NOT skip steps. Run all 5 even if data is sparse.
- Read the FULL OM — every page. Financials are usually on pages 15-25.
- If the OM has multiple NOI scenarios (current, transition, pro forma), include ALL of them.
- Always calculate NNN-equivalent rent for Gross leases by subtracting OpEx/SF.
- For Florida deals: flag that property taxes will reassess on sale.
- For unpriced deals: note "UNPRICED" and anchor price sensitivity to market avg $/SF.
- After the scorecard, ask: "Want me to dig deeper on this one, or is it a pass?"

## Knowledge Base Paths

- Screening: `/home/zjlammotta/projects/knowledge-base/real-estate-fund/wiki/deal-screening-criteria.md`
- Comps: `/home/zjlammotta/projects/knowledge-base/real-estate/wiki/target-market-comps-database.md`
- Market analysis: `/home/zjlammotta/projects/knowledge-base/real-estate/wiki/target-market-analysis.md`
- Flex vacancy: `/home/zjlammotta/projects/knowledge-base/real-estate/wiki/small-bay-data-vault.md`
- PM vendors: `/home/zjlammotta/projects/knowledge-base/real-estate-fund/wiki/property-management-vendor-list.md`
- Market deep dives: `/home/zjlammotta/projects/knowledge-base/real-estate/wiki/{market}-market-summary-*.md`
