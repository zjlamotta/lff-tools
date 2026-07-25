---
name: tax-bill-parse
description: Parse a county property tax bill or DTE form, identify special structures (TIF / abatement / PILOT / special districts), and project sale-triggered reassessment risk by state. Output is a tax-analysis memo that can stand alone OR feed Section 6 (Critical Risk) of `/om-deep-dive`. Use when a tax bill PDF lands in inbox for a new deal, OR when an OM's pro forma tax line needs to be stress-tested against reassessment. Liberty Office Park (OH TIF + sexennial cycle) is the canonical example.
---

# Tax Bill Parse — Reassessment & Special-Structure Analysis

Property tax is one of the most under-modeled line items in any OM. This skill takes a county property tax PDF (or DTE / equivalent assessment form), parses the components, identifies special structures (TIF, abatement, PILOT, special assessment districts), and projects sale-triggered or cycle-driven reassessment risk for the deal's specific state. Output ties back to NOI impact + value erosion at constant cap rate.

**Critical context:** Different states have radically different reassessment mechanics. The skill must apply state-specific rules — getting this wrong is a Liberty-scale underwriting miss.

## When to use this skill

- A county tax bill PDF / assessment form lands in inbox for a deal being underwritten
- OM's pro forma tax line is unclear, suspiciously low, or doesn't model reassessment
- `/om-deep-dive` Section 6 (Critical Risk) is being built and the deal's critical risk is tax-related
- A TIF, abatement, or PILOT structure is suspected (line item shows abated tax or unusual rate)

## Inputs Required

- **Tax bill PDF** (current year, prior year if available for trend)
- **Asset asking price / purchase price** (to compute effective rate on FMV)
- **State / county / jurisdiction**
- **OM's pro forma tax line** ($/yr it assumes) — for the OM-comparison step

If a prior-year bill is available, request it — year-over-year trend tells you whether the asset is mid-cycle, recently reassessed, or in a stable holding pattern.

## Step 1: Parse the Tax Bill

Extract the following fields from the PDF (different states label them differently — generalize):

| Field | What to look for | Examples |
|---|---|---|
| Parcel ID / Tax ID | Unique identifier | "012-345-678" |
| Assessed Value (AV) | What the county currently values | $1,200,000 |
| FMV / Appraised Value | County's current fair market value estimate (often distinct from AV — most states assess at fractional rate) | $3,000,000 at 40% AV ratio |
| Land vs. Building split | If shown | $400K land / $800K building |
| Mill Rate / Millage | Total levy expressed per $1,000 of value | 45.6 mills |
| Tax Year Total ($) | Year's total tax owed | $54,720 |
| Special Assessments | Separate line items beyond ad valorem | Sewer, road, fire district |
| Exemptions / Abatements | Any structure reducing taxable base | "Hamilton-Mason TIF" $XX,XXX abated |
| TIF / PILOT Notation | Specific district mention | Often a footnote on bill |
| Last Reassessment Date | Important for cycle states | "Last full reappraisal: 2023" |

**If the PDF is image-based (scanned), the parse may require OCR.** Use Bash + `pdftotext` or similar; flag if extraction is ambiguous.

**Compute immediately:**
- **Effective tax rate on AV** = Total tax / Assessed Value
- **Effective tax rate on FMV** = Total tax / county's FMV estimate
- **Effective tax rate on asking** = Total tax / asking price (← this is the LFF-relevant number)
- **$/SF tax burden** = Total tax / building NRSF

## Step 2: Apply State-Specific Reassessment Rules

This is the most important step. Different states have radically different mechanics; misapplying one to another is the kind of miss that costs $200K/yr in NOI.

### Texas (TX)

**Rule: Sale-triggered reassessment to market.**
- Per saved memory `feedback_tx_reassessment.md`: ALL DFW deals UW tax at **purchase price × 1.8%**, NOT seller's basis.
- Sale of asset triggers reassessment to the sale price in the next assessment cycle (typically Jan 1 following close).
- Seller's last-year tax is essentially meaningless for buyer underwriting.
- **Ryan LLC** is the first call for protest work (saved in memory).

**Stress test for TX deals:**
- Buyer Y1 tax = purchase price × 1.8% × (1 − protest haircut, typically 5-15%)
- Compare to OM pro forma — if OM is using seller's basis, NOI is overstated by the delta
- $200K/yr hit on $10M deal at delta is common; can flip a deal from go to no-go

### Ohio (OH)

**Rule: Sexennial reappraisal cycle + triennial mid-cycle update; sale price is a data point but NOT immediate trigger.**
- Counties reassess every 6 years (sexennial reappraisal) with a mid-cycle update every 3 years (triennial update).
- Sale price will be used in the NEXT cycle's valuation — not immediately.
- Butler County (Liberty Office Park) example: last full reappraisal 2023 (median +37% increase). Next triennial ~2026, next full ~2029.
- **The sale price becomes a comp** for assessor in next cycle. If asset trades above current AV, expect upward adjustment.
- Ohio also allows formal protest via Board of Revision.

**Stress test for OH deals:**
- **Current tax:** Most recent bill amount (proper baseline)
- **Y2-3 (mid-cycle update):** Likely +15-30% if market values rose since last cycle
- **Y5-7 (full reappraisal):** Likely +30-60% from last cycle baseline if market continues moving
- Each $/yr increase → $/cap rate erosion. E.g., $30K/yr tax bump at 7.68% cap = $390K of value erosion.

### New York (NY)

**Rule: Not sale-triggered for commercial; grievable annually.**
- Commercial property is assessed by town/village (Long Island) or NYC DOF (NYC).
- Sale does not directly trigger reassessment, but recent sale is a strong comp the assessor can cite.
- **Grievance work is the alpha** — per `project_130r_tax_reassessment.md`, 130R Huntington grieved from 10,800 → 8,650 = 20% reduction in AV. Combined RE tax dropped from ~$69K to ~$63K.
- Suffolk County grievance window: typically May 15 (Grievance Day) annually.

**Stress test for NY deals:**
- **Current tax** — baseline from bill
- **Grievable savings** — if AV looks high vs market, model 10-20% grievance haircut (with attorney fees ~$3-5K)
- **Sale catalyst** — does NOT trigger reassessment but does set up next year's grievance argument either way

### California (CA)

**Rule: Prop 13 — assessed value reset to purchase price at sale, locked at 2%/yr after.**
- Sale of asset = immediate reassessment to purchase price.
- 1% base rate + local special assessments (often 0.1-0.5% extra) = total ~1.1-1.5% on purchase price.
- After sale, annual increase capped at 2% (massive long-term benefit to long-hold owners).
- **Buyer Y1 tax = ~1.2% of purchase price.** Long-time seller may be paying 0.3-0.5% of current FMV → buyer's Y1 NOI hit can be 2-3x what the OM shows if using seller's basis.

**Stress test for CA deals:**
- Always recompute from purchase price × ~1.2%. Never trust seller's basis.
- Long-hold seller scenarios mean massive tax step-up at sale.

### Florida (FL)

**Rule: Save Our Homes cap (3% or CPI cap) applies to PRIMARY RESIDENCE ONLY. Commercial reassessed annually at market.**
- Commercial / investment property reassessed annually based on county appraiser's market-value estimate.
- Sale doesn't necessarily change market value, but if asset trades materially above current AV, expect adjustment next year.
- Effective rates vary by county; Orange County ~1.8-2.2% on AV is common.
- Watch for tangible personal property tax (TPP) — separate line on commercial assessments.

**Stress test for FL deals:**
- Likely modest annual creep (3-10%/yr) plus catch-up after sale if asset trades above AV
- Plus annual TPP if equipment-heavy

### Other states (NJ, NC, SC, IN, TN, etc.)

Apply jurisdiction-specific rules if known; flag to user as needing local tax counsel research if unknown. The general framework:
- Is sale a reassessment trigger? (Y/N)
- What's the cycle? (annual / triennial / sexennial)
- Are there caps or limits on increases?
- Is there a protest/grievance mechanism?

## Step 3: Identify Special Tax Structures

These are the line items that mask future tax exposure. Surface them aggressively.

### TIF (Tax Increment Financing)

- **What it is:** A geographic district where increased tax revenue from new development is captured to pay off the development bonds (rather than going to general fund).
- **Risk:** TIFs eventually expire. When they do, taxes don't "go away" — they redirect to general fund. Per memory note from Liberty session 2026-05-14: "TIF expiration just redirects same dollar amount; real risk is REASSESSMENT to current market value."
- **What to surface:**
  - When does the TIF expire?
  - Is the property's current taxable value anchored to TIF-creation-year base, or stepped up over the TIF life?
  - At expiration, if the underlying assessment hasn't kept pace with market, there's a reassessment catch-up risk.
- **Liberty example:** Property sits in Hamilton-Mason TIF. The TIF itself doesn't reduce tax for buyer — but if AV is artificially low pending TIF expiration + Ohio cycle update, the buyer is exposed.

### Tax Abatement

- **What it is:** Direct reduction in tax owed for a fixed period (e.g., 10-year 50% abatement for industrial development in certain zones).
- **Risk:** Abatement runs off. Year 11 tax bill is 2x year 10.
- **What to surface:**
  - Abatement type (statutory or negotiated?)
  - Expiration date / runoff schedule (cliff vs. step-down)
  - Is buyer eligible to continue the abatement, or does sale trigger reset?
  - Pro forma tax line in years AFTER abatement runs off

### PILOT (Payment in Lieu of Taxes)

- **What it is:** Negotiated agreement where property owner pays a fixed amount (often via municipal corporation / EDC) in lieu of standard ad valorem tax. Common for industrial / nonprofit / development incentive deals.
- **Risk:** PILOT amount may be way below market tax. When PILOT expires, normal taxation resumes.
- **What to surface:**
  - PILOT amount vs what standard tax would be on current FMV
  - Term / expiration date
  - Transfer rules — does sale terminate the PILOT, or is it transferable?

### Special Assessment Districts

- **What:** Separate fees for specific services — sewer, water, fire, road, drainage, business improvement districts.
- **Risk:** Usually flat or growing slowly; not material individually but can stack to 10-20% of total bill.
- **What to surface:**
  - List each district and its current charge
  - Any escalators (e.g., bond pay-off schedule with 3%/yr increases)
  - Any pending special assessments (e.g., new sewer line planned)

### Mello-Roos (CA only)

- **What:** California Community Facilities District (CFD) special tax — funds infrastructure for new developments.
- **Risk:** Adds 0.5-2.0% of property value annually on top of base 1% Prop 13.
- **What to surface:**
  - Mello-Roos amount + bond payoff date (often 20-40 year terms)
  - Whether it's transferable to new owner

## Step 4: Build Reassessment Scenarios

Build a multi-year tax projection table specific to the state's mechanics. Format:

```
| Year | Trigger | Assessed Value | Effective Rate | Annual Tax | Δ vs Baseline |
|------|---------|----------------|----------------|------------|---------------|
| Y1   | Status quo | $X,XXX,XXX | X.XX% | $XX,XXX | — |
| Y2   | <reassess event> | $X,XXX,XXX | X.XX% | $XX,XXX | +$X,XXX |
| Y3   | <next event> | $X,XXX,XXX | X.XX% | $XX,XXX | +$X,XXX |
...
```

For each scenario row, translate to financial impact:
- **NOI hit** = $/yr increase
- **Value erosion at constant cap rate** = $/yr increase / cap rate
- **% of equity at risk** = value erosion / equity at close

**Always run at least 3 scenarios:**
- **Floor:** No reassessment, OM pro forma is correct
- **Base:** State's typical-cycle reassessment trajectory (e.g., OH mid-cycle update + full reappraisal)
- **Stress:** Aggressive reassessment to current FMV + buyer pays full freight (assume worst-case grievance fail)

## Step 5: Compare to OM Pro Forma

Critical comparison — this is where most underwriting misses happen.

```
| Line | OM Pro Forma | This Analysis | Δ |
|------|--------------|---------------|---|
| Y1 tax | $X,XXX | $X,XXX | $X,XXX |
| Y3 tax | $X,XXX | $X,XXX | $X,XXX |
| Y5 tax | $X,XXX | $X,XXX | $X,XXX |
| Annual escalation modeled | 3%/yr | X.X%/yr (per state cycle) | — |
| Reassessment event modeled in OM? | Y/N | — | — |
```

**Verdict:** Is the OM understating, overstating, or matching realistic exposure?
- If **understating** by >10% on cumulative tax exposure → flag as material adjustment to offer price
- If **matching** → carry forward in main pro forma without modification
- If **overstating** → opportunity to push back on OpEx assumptions in negotiation

## Step 6: Output — Tax Analysis Memo

Save to: `<vertical>/deals/<deal-slug>/tax-bill-analysis-<YYYY-MM-DD>.md`

Format:

```markdown
---
deal: <Deal Name + Location>
jurisdiction: <County, State>
parcel_id: <ID>
date_analyzed: YYYY-MM-DD
source_bill_year: YYYY
related:
  - <path to bill PDF>
  - <path to deal's analyst memo if exists>
---

# <Deal Name> — Tax Bill Analysis

## Headline

One sentence: current tax burden, key special structure (if any), reassessment posture.

**Net read** (1 paragraph): The single most important finding. E.g., "Current $100K bill is anchored to 2023 AV; with $8.05M sale price triggering Ohio's 2029 sexennial reappraisal, expect 30-60% tax increase = $30-60K/yr NOI hit = $390-780K value erosion at 7.68% cap."

## Current Tax Snapshot

| Metric | Value |
|---|---|
| Parcel ID | |
| Assessed Value | $ |
| County FMV | $ |
| Sale / Asking Price | $ |
| Total Annual Tax | $ |
| Effective Rate on AV | x.xx% |
| Effective Rate on Asking | x.xx% |
| $/SF Tax Burden | $ |
| Tax Year | |
| Last Reassessment | |

## Tax Bill Components

| Component | Amount | % of Total |
|---|---|---|
| Ad Valorem (base levy) | $ | XX% |
| <Special district 1> | $ | XX% |
| <Special district 2> | $ | XX% |
| <TIF / abatement note> | $ | XX% |
| **Total** | $ | 100% |

## Special Structures Identified

<List each TIF / abatement / PILOT / special district with: name, type, current $ impact, expiration date, transfer status.>

## Reassessment Risk (State: <State>)

**State-specific rule:** <1-2 sentence statement of the mechanics — e.g., "Ohio uses 6-year sexennial cycle with 3-year mid-cycle updates. Sale price becomes a comp for the next cycle but does not trigger immediate reassessment.">

**Multi-year projection:**

| Year | Trigger | AV | Rate | Tax | Δ |
|---|---|---|---|---|---|
| Y1 | | | | | — |
| Y2 | | | | | |
| Y3 | | | | | |
| ... | | | | | |

**Scenario summary:**

| Scenario | 7-yr Cum Tax | Cum Δ vs OM | Value Erosion @ Cap |
|---|---|---|---|
| Floor (OM correct) | $ | — | — |
| Base (typical cycle) | $ | +$ | -$ |
| Stress (aggressive reassessment) | $ | +$ | -$ |

## Comparison to OM Pro Forma

<Table showing OM's tax assumptions vs this analysis, year by year. Verdict: understating / matching / overstating.>

## Recommendation

- **Offer price adjustment** (if understated): -$XXX,XXX off price to compensate for under-modeled tax
- **DD asks**: Specific questions for broker / county assessor / tax counsel
- **Local tax counsel referral**: e.g., Ryan LLC for TX, named protest counsel for other states
- **Grievance / protest opportunity** if AV looks high vs comps

## Cross-references

- Deal analyst memo: <path>
- Saved memory references: [[feedback_tx_reassessment]], [[project_130r_tax_reassessment]], etc.
- Related KB wiki articles: <market-deep-dive paths>
```

## Step 7: Feed into Other Skills

If a deal's `om-deep-dive` is being built or has been built:
- This tax analysis becomes **Section 6 (Critical Risk)** of that memo IF tax-reassessment is the deal's #1 risk
- Add the recommended offer-price adjustment to **Section 12 (Pricing Summary)** of the analyst memo
- Add tax-specific broker questions to the `<deal>/broker-questions-running-list.md` (Section 5: Financials Detail)

If `om-deep-dive` doesn't exist yet, this analysis stands alone and feeds into it when built.

## Cross-references the skill should read

- `MEMORY.md` — for `feedback_tx_reassessment.md`, `project_130r_tax_reassessment.md`, `project_market_tiering.md`
- Deal's existing memo or screen output if any
- State / county tax authority guidance (web search if needed for jurisdictions outside the saved patterns)

## Common Pitfalls

- **Don't apply TX rules to OH deals (or vice versa).** Sale-triggered vs cycle-driven reassessment are fundamentally different — getting this wrong costs the deal.
- **Don't accept OM's 3%/yr escalation assumption blindly.** That's the standard placeholder; doesn't model cycle reassessments. For OH/NY/CA/FL deals, recompute with state mechanics.
- **Don't forget Ohio's sexennial timing.** The Liberty memo noted: "2023 was Butler County's full reappraisal; next triennial 2026, next full 2029." A 7-year hold straddles BOTH events. Model both.
- **Don't conflate AV with FMV.** Most states assess at fractional rate (e.g., 35-40% of FMV). Stating "tax / AV = X%" without converting to tax / FMV understates effective burden.
- **Don't ignore special assessment districts.** They can add 10-20% on top of ad valorem and have their own escalation curves (bond payoff schedules).
- **Don't skip the grievance opportunity in NY.** Per 130R example, 20% AV reduction is achievable — model both scenarios in the projection.
- **Don't model TIF as "free money."** TIF expiration in 2-3 years with current AV not stepped up to market = reassessment catch-up risk. Surface it.
- **Don't model PILOT amounts as permanent.** Check expiration; many PILOTs end at 10 or 15 years.
- **Don't recommend an offer-price adjustment without sizing it.** "Material tax risk" isn't useful; "model $30K/yr base case adjustment = $400K value at 7.68% cap = offer $400K below ask" is useful.

## Test Examples

- **Liberty Office Park (Butler County, OH):**
  - Current tax: $100,328 (~1.24% on $8.05M asking)
  - Ohio sexennial cycle, last full 2023 (+37% county median), next triennial 2026, next full 2029
  - Hamilton-Mason TIF (does not reduce buyer's bill but anchors AV to a base; risk = catch-up at next cycle)
  - Base case: $30K/yr increase by Y5 → $390K value erosion at 7.68% cap
  - Stress case: $60K/yr → $780K value erosion
  - **Verdict in Liberty memo:** OM models 3%/yr escalation — likely UNDERSTATES. Adjust offer by $200-400K.

- **130R Huntington (Suffolk County, NY):**
  - Successful grievance 10,800 → 8,650 AV (-20%)
  - Combined RE tax: 2024-25 was $69K; 2025-26 is $62,717
  - Tax stop in lease at $58,637 — buyer eats anything above that
  - **Pattern: NY grievance work is material; always model both pre/post-grievance scenarios.**

- **DFW deal example (hypothetical $10M acquisition):**
  - Seller's basis (long-time owner): $6M AV at 2.5% = $150K/yr
  - Buyer Y1 (per `feedback_tx_reassessment`): $10M × 1.8% = $180K/yr
  - $30K/yr understatement in OM = $400K value erosion at 7.5% cap = the kind of miss that flips a deal

## Updates / Iteration

State-specific reassessment patterns evolve (legislation, cycle changes, new TIF programs). When working on a deal in a state not previously analyzed, add findings to a new `<state>-tax-rules-<date>.md` reference in the KB so the next deal benefits from the work. The `Step 2` section of this skill should be updated as the KB accumulates state-specific knowledge.
