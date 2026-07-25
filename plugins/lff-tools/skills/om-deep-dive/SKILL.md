---
name: om-deep-dive
description: Build a full institutional-grade analyst memo on a deal that has cleared `om-screen` with Pursue. Output is a 12-section memo (headline, building, location, rent roll + tenant credit, financials, critical risk surface, market context, LFF fit, underwriting math, decision framework, broker Qs, pricing summary). Use when user has decided to underwrite a deal seriously, has the OM + supporting docs in hand, and wants a memo Paul + LPs can review. NOT a quick screen — that's `om-screen`.
---

# OM Deep Dive — Full Analyst Memo

A deal cleared `om-screen` (or was triaged manually as worth deeper work). This skill produces a comprehensive analyst memo modeled on `real-estate/deals/liberty-office-park/analyst-memo-2026-05-14.md` — the canonical reference. The memo should be Paul-and-LP-ready in tone and depth: opinionated, fully cited against KB sources, honest about risks, and ending with a clear "my honest recommendation."

## Inputs required

- **OM PDF** (full marketing package from broker)
- **Rent roll** (separate file if provided)
- **Any supporting docs** dropped in inbox (tax bills, leases, lease abstracts, environmental, prior PCA, comp packets)
- **Deal name + slug** (e.g., "Liberty Office Park" → `liberty-office-park`)
- **Vertical** (real-estate / marinas / data-centers / etc.)

## Workflow overview

1. **Build the memo** — 12 standard sections (see Section Spec below)
2. **Cross-reference KB liberally** — market deep-dives, comps DB, screening criteria, saved memory
3. **Surface deal-specific risks** — every deal has at least one structural risk that the OM doesn't model adequately
4. **Recommend next actions** — typically `broker-questions` to spin up the Q-list, and specific DD asks
5. **Save to** `<vertical>/deals/<deal-slug>/analyst-memo-<YYYY-MM-DD>.md`
6. **Tell user** memo location, headline verdict, and recommend running `/broker-questions` next

## Section Spec — 12-section memo template

Use these section headers in this order. Adapt content depth to deal complexity but keep section sequence consistent across deals so Paul + Zach build pattern-recognition.

### Section 1: Headline

- 1-2 sentence asset description (size, location, tenant count, vintage, asking, $/SF, cap rate)
- 1-paragraph **Net Read** in bold — clear opinionated take, not hedged. State whether asset/location/pricing/structure are favorable or not. Mention the specific structural risk that drives the next 10 sections.

### Section 2: The Building

- **Spec table** with: NRSF, office %, warehouse %, acres, year built, construction, roof type/age, clear height, loading (docks + drive-in), parking ratio, zoning, utilities
- **Industrial-flex assessment paragraph** — Is this REAL industrial flex (high clear, loading, tilt-up)? Or office-with-warehouse pretender? Underlying re-leasability to traditional small-bay industrial users is the downside-protection question.
- **CapEx flag paragraph** — Identify items likely to need replacement within hold (roof, HVAC, parking, mechanicals). Specifically critique the OM's capex reserve assumption (usually $0.10-$0.20/SF/yr — usually too low for any deal with a >15-year-old roof).

### Section 3: Location

- **Drive-time table** (I-x highway, MSA core, neighboring metros)
- **Neighborhood character paragraph** — Is this an industrial corridor or retail/office epicenter? This drives re-leasing thesis: industrial users vs. high-rent flex/retail-adjacent users.
- **Submarket momentum bullets** — recent / upcoming developments, anchor projects, demographic profile. Pull from `real-estate/wiki/<market>-market-deep-dive.md` if exists; also note Steven's / partner local knowledge if relevant.

### Section 4: Rent Roll & Tenant Deep Dive

- **Rent roll snapshot table** with: Suite, Tenant, SF, %SF, Base $/SF, CAM, All-in $/SF, %Rev, Expires, Lease Type, Guarantor
- **Tenant credit deep dive** — for EACH tenant (or at minimum top tenants by SF):
  - Entity background (founded, size, what they do)
  - Lease history (when last renewed, term remaining, escalations)
  - Lease structure ($/SF base, lease type NNN/MG/Gross, PG status, security deposit, special clauses like percentage rent)
  - **Credit assessment** — your read on this tenant's stickiness, with named risks
  - Use D&B / web research signals where available (use `tenant-credit` skill if heavy lifting needed)
- **Tenant Concentration table** — split by: SF concentration on top 2 tenants, revenue concentration, industry exposure (entertainment vs. industrial vs. office), PG vs. no-PG split
- **Surface concentration risk explicitly** — "Loss of either Tenant X or Tenant Y would gut NOI" type statements

### Section 5: Financials — Pro Forma Analysis

- **Year 1 Underwriting table** — From OM: Potential Base Rent, Vacancy, Total Rental Revenue, Expense Recoveries, EGR, Total OpEx, NOI. With $/SF columns.
- **Operating Expenses Breakdown table** — Each line item with $/yr + $/SF, and a Notes column flagging anything anomalous (high taxes, missing items like reserves, mgmt fee % off-market, etc.)
- **Pro Forma Cash Flow Over Hold table** — Multi-year (typically 7-11 yrs) with EGR, NOI, Lease/CapEx, Cash Flow columns. **Flag any year where Cash Flow drops materially below baseline** — these are syndication-distribution challenges.
- **Market Rent Assumptions table** — What the broker's pro forma assumes for new + renewal rents; renewal probability assumptions
- **Mark-to-Market: Realistic vs. Aspirational** — The OM headline M2M number is almost always overstated. Build your own per-tenant M2M analysis at end of each lease term, weighted by renewal probability and tenant stickiness. Compare to OM's headline. State the realistic uplift in $/SF and $ terms.

### Section 6: Critical Risk — Deal-Specific

This section is the **single most important deal-specific risk** that the OM doesn't model adequately. Title it explicitly (e.g., "Critical Risk: Ohio Property Tax Reassessment" or "Critical Risk: Bulkhead Replacement Cost"). For every deal, there is at least one.

Common patterns by jurisdiction / asset type:
- **DFW / TX deals:** Reassessment on sale (purchase price × 1.8%, NOT seller's basis). Per `feedback_tx_reassessment.md`. Stress test with $/yr NOI impact.
- **Ohio / Cincinnati deals:** Sexennial reappraisal cycle; sale data point used in next cycle even if not immediate trigger. Model 2026/2029 reassessment scenarios.
- **TIF / abatement deals:** When does it expire? What's the reassessment trigger? See Liberty Office Park experience.
- **Marinas / waterfront:** Bulkhead replacement cost ($300K-$1M+), dredging frequency, fuel tank remediation.
- **Older industrial:** Phase I environmental, asbestos, USTs.
- **CMBS / loan assumption deals:** Existing debt structure restrictions, lockout periods, defeasance costs.
- **Co-located / DC deals:** Power capacity, utility transformer wait times.

For each critical risk: **size the financial impact at multiple scenarios**, compare to OM's modeled exposure, and state the implied value erosion at constant cap rate.

### Section 7: Market Context

- **MSA-level snapshot** from KB (`real-estate/wiki/<market>-deep-dive.md` or similar) — vacancy, rent growth, cap rate trend, big-picture fundamentals
- **Submarket-level** — vacancy in subject submarket, recent leasing/sales velocity, comp activity
- **Cap rate verification** — Cite recent flex/industrial sale comps in the MSA at similar size/age. Is the asking cap rate "AT market," "below market," or "above market"? This is the negotiating-room read.
- Cross-reference any partner / Steven / sponsor local knowledge from memory

### Section 8: LFF / Syndication Fit Assessment

- **Pros table** — factor + read pairs (e.g., "Asset type — flex/light industrial" → "Fits LFF Industrial mandate per [[feedback_underwriting_floor_ceiling]]")
- **Cons table** — same format. Surface bandwidth, deal size vs. typical raise, complexity, tenant mix anomalies, etc.
- Cite memory entries directly (e.g., `[[project_market_tiering]]`, `[[user_first_business_acquisition]]`, `[[feedback_syndication_leverage]]`)

### Section 9: Underwriting Math

- **As asked** scenario — At ask, with current leverage assumptions (65% LTV per `feedback_syndication_leverage.md` for syndication base case; 80% only marina-specific). Build a Y1 cash-flow-to-equity table. Flag any year in the hold where cash flow goes meaningfully negative or near-zero.
- **What I'd actually offer** scenarios — 3 price points: Aggressive opener (8-12% below ask), Likely landing (5-7% below ask), Asking. With implied cap rate at each. Brief rationale list for each price point.

### Section 10: Decision Framework

- **Reasons to PURSUE** — numbered list, opinionated
- **Reasons to PASS (or wait)** — numbered list, opinionated
- **Recommended Next Steps** if pursuing — specific DD asks (Argus model, tenant financials, environmental, property condition, etc.)
- **Sponsor Decision** — your honest recommendation, named (e.g., "Send a non-binding LOI at $X-Y, condition on Z-day exclusive DD"). Tie to bandwidth, parallel deals, LP appetite.

### Section 11: Open Questions for Broker

- Lead-in: explicit pointer to the `broker-questions` skill to spin up the full Q list
- In this section, just hit the **10 highest-priority questions** (the ones whose answers materially shift the verdict). Pull these from the deep-dive analysis above.
- Note: full broker Q list (33+ across 8 sections) lives in the separate `<deal-slug>/broker-questions-running-list.md` file produced by `/broker-questions`.

### Section 12: Pricing Summary

Final table with:
- Asking ($ / $/SF / cap)
- Aggressive opener ($ / $/SF / cap)
- Likely landing ($ / $/SF / cap)
- Equity required at likely landing
- Year 1 cash-on-cash at likely landing
- Year 1 cash distribution at likely landing

End with **one sentence** that captures the pricing-discipline view: "At $X, this is a solid LFF deal. At $Y, returns are tight and execution risk dominates."

## Cross-references the skill should read

For every deep-dive, read at minimum:
- `real-estate-fund/wiki/deal-screening-criteria.md` — market-specific benchmarks
- `real-estate/wiki/target-market-comps-database.md` — comp data
- `real-estate/wiki/<market>-deep-dive.md` if exists — submarket context
- `MEMORY.md` — for market tiering, partner local knowledge, saved feedback (TX reassessment, syndication leverage, floor/ceiling)
- The deal's OM, rent roll, and any supporting docs

If a relevant wiki article doesn't exist for the deal's market, flag that gap and suggest running `/refresh-market` to build one before completing the memo.

## Tone + writing style

- **Opinionated, not hedged.** The Liberty memo says "Solid asset in an exceptional location with REAL but modest value-add upside. NOT a slam-dunk LFF Industrial fit." That's the right voice. Take a stance.
- **Honest about risks.** Don't bury concentration risk in section 4; surface it. Don't gloss over Y4 cash flow dips; flag them.
- **Numerical specificity.** Don't say "high taxes" — say "$1.31/SF, 1.24% effective rate, vs $0.80-$1.10/SF benchmark for Cincinnati flex."
- **Cite KB sources.** If a comp rate or benchmark comes from KB, link it `[[]]`-style.
- **Don't over-format.** Tables for data; prose for analysis. Avoid bullet-list overuse.
- **End sections with implications**, not summaries. The reader wants "so what?"

## Save location

`<vertical>/deals/<deal-slug>/analyst-memo-<YYYY-MM-DD>.md`

If `<deal-slug>` directory doesn't exist, create it. If a prior `analyst-memo-*.md` exists for the same deal, do NOT overwrite — append a date suffix and tell user there's a prior memo.

## After saving

Tell the user:
1. **File path** of the new memo
2. **Headline verdict** (one sentence)
3. **Top 3 risks surfaced** that drive the verdict
4. **Recommended next action**: "Run `/broker-questions` to spin up the full Q-list for diligence"
5. **Estimated equity required + LP raise context** so user can sanity-check fit

## Common Pitfalls

- **Don't write a 12-section memo when the deal is actually a Pass.** If during deep-dive you uncover a hard reject the screen missed, STOP, write a 3-paragraph "Why I'm switching this to Pass" note, save it as `pass-rationale-<date>.md` in the deal folder, and recommend `/deal-log` to file it. Don't waste the 60 min on a deal that should die.
- **Don't take the OM's M2M number at face value.** Build your own bottom-up per-tenant M2M analysis. 80% of OMs overstate this.
- **Don't accept the OM's property tax assumption.** Reassessment, TIF expiration, abatement run-off — every state and county has its own structure. Run a 5-year forward stress test on this line specifically.
- **Don't underbudget capex.** OMs typically assume $0.10/SF/yr capex reserve. If roof, HVAC, or parking is mid-life, the realistic reserve is 3-10× that.
- **Don't assume PG = real PG.** Verify it's enforceable, not just stated. CRAV's two PGs in Liberty are worth a lot more than Get Air's "LLC only."
- **Don't skip Section 6 (Critical Risk).** Every deal has one. If you can't find it, you didn't look hard enough.
- **Don't use Tier 1 leverage assumptions for non-syndication deals.** Marina was 80% seller note (specific structure). Syndication base is 65% LTV per `feedback_syndication_leverage.md`.
- **Don't conflate analyst-memo and broker-questions.** The memo has a TOP-10 broker-question list; the full 33+ Q tracking list is a separate file via the `/broker-questions` skill.

## Test Reference

**Liberty Office Park** (`real-estate/deals/liberty-office-park/analyst-memo-2026-05-14.md`) is the canonical example. Re-reading it should make every section spec above feel natural. If output diverges materially from that structure or tone, rebalance toward Liberty.

Specifically, the Liberty memo's "Critical Risk: Ohio Property Tax Reassessment" section (Section 6) is the ideal pattern: cites Ohio assessment law specifics, names Butler County's last cycle, models 3 scenarios (no reassessment / mid-cycle update / full reappraisal), translates to $/yr tax increase, and converts to value erosion at constant cap. **Mimic this depth on the critical risk for every deal.**
