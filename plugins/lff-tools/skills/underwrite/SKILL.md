---
name: underwrite
description: The acquisition FRONT DOOR — produce the one-page data scorecard AND a bid recommendation for an industrial/flex deal from an OM/flyer. Fit-check, extract, screen, comps, capital stack, price sensitivity, a quick levered-IRR/equity-multiple approximation, and an OFFER / HOLD / PASS gate with an opening-bid and walk-away number you can act on before any Excel model. Use when a deal lands and you need a number to offer off. `om-deep-dive` (IC memo) and your full Excel model come LATER, only after an offer sticks (DD confirmation). For a blind-OM firehose triage only, use `om-screen`.
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

**Table D: Quick Levered IRR / Equity Multiple (5-yr approximation — no Excel needed)**

This is a deliberate approximation so you can bid off the scorecard. It is NOT the full model — the Excel
model is a post-offer confirmation. State the assumptions explicitly every time:

- Hold: 5 years
- Forward NOI: Year 1 = Stabilized NOI; grow 3%/yr (or use the rent roll's weighted escalations if shown).
  If the deal has a lease-up ramp, note the early-year CF is lighter than a flat stabilized figure implies.
- Exit cap: entry going-in cap, held FLAT (no compression credit) as base; also show exit +50 bps as downside.
- Cost of sale: 2% of gross sale price.
- Debt: from Step 4 (65% LTV, rate, 25-yr amort, 5-yr term). Loan balance at exit = amortized balance after 60 payments.
- Equity: Total Basis − Loan (Step 4).

Levered cash-flow stream, computed per price point:
- Year 0: −Equity
- Years 1–5: `NOI_year − Annual Debt Service`, where `NOI_year = Stabilized NOI × 1.03^(year−1)`
- Year 5 also adds Net Sale = `(Stabilized NOI × 1.03^5 ÷ exit cap) × (1 − 0.02) − Loan Balance_Y5`
- Quick IRR = IRR of that stream. Quick EM = `(Σ Years 1–5 CF + Net Sale) ÷ Equity`.

```
  | Price | $/SF | Equity | Quick IRR | Quick EM | vs 20-22% IRR / 2.0-2.3x EM |
  | Asking / Anchor | | | | | |
  | -10% | | | | | |
  | -15% | | | | | |
  | -20% | | | | | |
```

Then compute the single **walk-away price** = the price where Quick IRR ≈ 21% (interpolate from the table).
This feeds the bid gate in Step 7.

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
  Quick levered IRR (5-yr approx): {X}% {vs 20-22% target}  |  Quick EM: {X}x
  ═══ BID GATE: {OFFER at $X — walk-away $Y | HOLD — pencils ≤$Y | PASS — no pencil} ═══
  (Your Excel model confirms this post-offer; not run here)

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

### Step 7: OFFER / HOLD / PASS — the bid gate

This is the front-door output — the number to act on before any Excel model. Using Table D + comps (Step 3):

1. **Walk-away** = price where Quick IRR ≈ 21% (midpoint of the 20-22% target).
2. **Opening bid** = walk-away × 0.875 (open ~12.5% below walk-away — pair a low opener with hard/clean
   terms, per the LFF bid-anchor strategy). Round to a clean number.
3. **Gate** (compare walk-away to asking / likely-clearing price):
   - **OFFER** — walk-away within ~10% of asking (target returns at a plausibly winnable price).
     Output: `OFFER at $[opening] — walk-away $[walk-away]`.
   - **HOLD** — walk-away 10–25% below asking (pencils only at a real discount).
     Output: `HOLD — pencils ≤ $[walk-away]; offer low or revisit if it sits`.
   - **PASS** — walk-away >25% below asking, or can't clear ~15% IRR at any realistic price.
     Output: `PASS — doesn't pencil at a realistic price`.
4. **Sanity-check the bid against comps + floor/ceiling spread (Step 3)** — never recommend offering above
   what the basis comps support, even if the IRR math allows it. Bid off actuals, not the OM pro forma.

The scorecard is what you bid off. `om-deep-dive` (IC memo) and the full Excel model come **after** an offer
sticks, as DD confirmation — not before.

## Rules

- The scorecard's job IS to hand Zach a bid number — give the **OFFER / HOLD / PASS** gate with an opening
  bid and walk-away. This is a bid recommendation to act on, not a final buy commitment (Zach still decides).
- Do NOT defer returns to "run full model" — approximate the levered IRR here (Table D). The Excel model is a
  post-offer confirmation, not the front-door filter.
- Do NOT ask clarifying questions during the process. Use "NOT PROVIDED" for missing data.
- Do NOT skip steps. Run all of them even if data is sparse.
- Read the FULL OM — every page. Financials are usually on pages 15-25.
- If the OM has multiple NOI scenarios (current, transition, pro forma), include ALL of them.
- Always calculate NNN-equivalent rent for Gross leases by subtracting OpEx/SF.
- For Florida deals: flag that property taxes will reassess on sale.
- For unpriced deals: note "UNPRICED" and anchor price sensitivity to market avg $/SF.
- End with the bid gate in one line: `OFFER at $X — walk-away $Y`, `HOLD — pencils ≤ $Y`, or `PASS`. Then
  note that `om-deep-dive` (IC memo) and the full Excel model are the post-offer steps if it advances.

## Knowledge Base Paths

- Screening: `/home/zjlammotta/projects/knowledge-base/real-estate-fund/wiki/deal-screening-criteria.md`
- Comps: `/home/zjlammotta/projects/knowledge-base/real-estate/wiki/target-market-comps-database.md`
- Market analysis: `/home/zjlammotta/projects/knowledge-base/real-estate/wiki/target-market-analysis.md`
- Flex vacancy: `/home/zjlammotta/projects/knowledge-base/real-estate/wiki/small-bay-data-vault.md`
- PM vendors: `/home/zjlammotta/projects/knowledge-base/real-estate-fund/wiki/property-management-vendor-list.md`
- Market deep dives: `/home/zjlammotta/projects/knowledge-base/real-estate/wiki/{market}-market-summary-*.md`
