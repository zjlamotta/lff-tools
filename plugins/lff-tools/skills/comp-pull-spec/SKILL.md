---
name: comp-pull-spec
description: Generate the optimal CoStar / CompStak / LoopNet filter parameters for a deal's sale + lease comp pull. Outputs a structured spec with primary filters, a 3-tier fallback ladder if primary returns too few comps, secondary markets to expand into, and a list of what to extract from each comp. Use when user is about to pull comps for an active deal and wants to avoid the "two failed CoStar pulls = 1 hour wasted" problem. Liberty Office Park (Cincinnati flex pull) is the canonical example of why this matters.
---

# Comp Pull Spec — Optimal Filter Parameters

The cost of a bad CoStar pull is a wasted hour and a comp set that misleads underwriting. This skill builds the right filter parameters BEFORE the pull, with a fallback ladder if the primary filter is too tight.

## When to use this skill

- User has a deal in active underwriting and needs sale + lease comps for the analyst memo
- `/om-deep-dive` is being built and Section 7 (Market Context) or Section 9 (Underwriting Math) needs comp validation
- A new market (no prior comps work in KB) requires building a comp methodology
- User says "pull comps for X" or "I need comps for this deal"

## Inputs Required

- **Asset type:** small-bay industrial / flex / warehouse / office / retail / mixed
- **Market:** MSA name (e.g., "Cincinnati MSA")
- **Submarket:** Specific (e.g., "West Chester / Tri-County")
- **Subject size:** Building NRSF
- **Subject age:** Year built / vintage range
- **Subject occupancy:** % at time of sale
- **Subject lease structure:** NNN / MG / Gross (for lease comps)
- **What's being benchmarked:** $/SF, cap rate, in-place rent, market rent, or all of the above

## Step 1: Pick the Right Platform

| Platform | Best for | Cost / Access | Use When |
|---|---|---|---|
| **CoStar** | Sale + lease comps, deep historical, broker-quality data | Paid (premium) | Primary tool for any serious DD; required for institutional underwriting |
| **CompStak** | Lease comps — actual signed rates, concessions, TI/LC packages | Paid or contributor-model | Verifying lease rates beyond CoStar's listing-rate (often inflated); rent-roll sanity checks |
| **LoopNet** | Asking-price intelligence, marketing-driven listings | Free / cheap | Sourcing live opportunities; NOT for sale-comp validation |
| **County Assessor / Recorder** | Sale-price verification | Free, jurisdiction-specific | Cross-check a CoStar sale figure if anomalous; verify recording date |
| **CompStak Pro / RCA / MSCI Real Capital** | Institutional sale comps + cap rate analytics | Enterprise | For deals where CoStar's comp depth is thin (esp. small-bay industrial) |

**Default stack:** CoStar primary → CompStak for lease validation → County records for sale verification on anomalous transactions.

## Step 2: Primary Filter Spec

Build the filter as a structured spec. For each filter dimension, pick a value AND a tolerance band.

### Geographic Radius

| Subject Market Density | Primary Radius | Notes |
|---|---|---|
| **Dense MSA core** (e.g., Long Island, NJ, Northern VA) | 3-5 miles | Submarket-specific; broader expands to wrong submarket |
| **Mid-density MSA** (e.g., Columbus, KC, Cincinnati, Charlotte) | 5-10 miles | Most LFF Tier 1 markets fall here |
| **Suburban / exurban** (e.g., Greenville-Spartanburg, Greensboro) | 10-20 miles | Fewer transactions; need wider net |
| **Rural / tertiary** | 25-40 miles or expand to neighboring MSA | Edge case |

Use the **subject's lat/long or address as anchor**, not the city centroid — submarket placement matters.

### Product Type

CoStar categories that map to LFF deal profile:
- **Industrial → Flex** — closest match for small-bay multi-tenant
- **Industrial → Warehouse / Distribution** — for clear-span warehouse
- **Industrial → Light Manufacturing** — if tenant uses include fabrication
- **Industrial → R&D / Lab** — usually NOT what we want (higher office %)

For subjects that are 30-40% office + 60-70% warehouse with multi-tenant, **filter on BOTH Flex AND Warehouse / Distribution** to get sufficient comp universe.

### Size Range

Default rule: subject SF × 0.5 to subject SF × 2.0

| Subject Size | Comp Size Range | Why |
|---|---|---|
| 20,000 SF | 10,000-40,000 SF | Captures meaningful flex range |
| 50,000 SF | 25,000-100,000 SF | Wider — fewer comps at exact size |
| 75,000 SF | 40,000-150,000 SF | Mid-size flex — institutional buyer pool |
| 100,000+ SF | 50,000-200,000 SF | Approaching institutional / REIT comp set |

**If subject is below 20K SF**: drop to 5K-50K SF range. Below 10K SF is hard-reject per LFF screen anyway.

### Vintage / Year Built

- **Tight band: subject year ± 10 years** (e.g., 2007 subject → 1997-2017 comps)
- **Loose band: subject year ± 20 years** (fallback if tight is thin)
- **Exclude**: pre-1970 unrenovated (per LFF hard reject); pre-1980 with no major reno

### Sale Date Window

- **Primary: trailing 12 months** — most comparable to current market
- **Secondary: trailing 24 months** — fallback when 12mo is thin
- **Tertiary: trailing 36 months** — last resort; annotate that pre-current-cycle comps may be stale

For **cap rate verification specifically**: ALWAYS prefer most recent 6-12 months. Cap rates compress / expand quickly with rate environment.

### Occupancy at Sale

- **Primary: 90%+ occupied at sale** — comparable to LFF deals
- **Secondary: 75%+ if subject is 100%** — broader; flag that vacant-at-sale comps need adjustment
- **Exclude: <50% occupied** — different deal type (value-add takedown)

### Lease Structure (for lease comps only)

- **Primary: NNN** — most LFF deals
- **Secondary: include MG** if NNN universe thin, but adjust effective $/SF
- **Exclude: Full-service gross** unless explicitly looking for office/medical

### Building Class

- **Industrial Class B+** or Class A for most LFF flex
- **Class C** acceptable if subject is C (vintage / no recent reno)
- **Exclude Class A pure-warehouse** unless subject is institutional-grade

## Step 3: Fallback Ladder

If primary filter returns **fewer than 5-8 comps**, expand systematically. Don't dump constraints all at once — relax one at a time and note what was relaxed.

```
Primary filter → < 5 comps
  ↓
Tier 1 expand: Widen geography by 50% (5mi → 7.5mi; 10mi → 15mi)
  ↓ still < 5
Tier 2 expand: Widen vintage band (±10yr → ±20yr)
  ↓ still < 5
Tier 3 expand: Lengthen time window (12mo → 24mo → 36mo)
  ↓ still < 5
Tier 4 expand: Loosen product type (flex → flex + warehouse + light mfg)
  ↓ still < 5
Tier 5 expand: Expand to secondary market(s) — see Section 4 below
  ↓ still < 5
Decision: Flag comp universe as too thin. Note to user that any cap rate / $/SF benchmark will have wide uncertainty bands.
```

**Document which tier was reached** in the output spec. A "Tier 3" comp set warrants different confidence than a "Primary" one.

## Step 4: Secondary Markets (Fallback Expansion)

When primary market comp universe is too thin, expand to similar-character secondary markets. Map by similarity rather than geography.

| Subject Market | Similar-Character Secondary Markets | Why |
|---|---|---|
| **Cincinnati flex** | Columbus, Indianapolis, Louisville | Midwest mid-density flex; similar cap rate profiles |
| **Tampa flex** | Orlando, Jacksonville | FL flex submarkets w/ similar tenant demand |
| **Nashville flex** | Charlotte, Raleigh-Durham | Southeast knowledge-economy MSAs |
| **Columbus flex** | Cincinnati, Indianapolis, KC | Midwest mid-cap-rate flex |
| **KC flex** | Indianapolis, Columbus, OKC | Lower-cost Midwest with logistics anchors |
| **Charlotte flex** | Raleigh-Durham, Nashville | Southeast growth markets |
| **Long Island flex** | NJ (Bergen / Middlesex), Westchester County | Tri-state high-rent flex |
| **Richmond flex** | Greensboro, Greenville-Sptbg, Hampton Roads | Carolinas-Virginia secondary growth |
| **DFW flex** | Houston, Austin, San Antonio | TX-specific (note: TX tax reassessment per `[[feedback_tx_reassessment]]` skews comps) |
| **Greenville-Sptbg flex** | Greensboro, Charlotte, Asheville | Carolinas mid-density |

For markets not on this list, derive similarity from: cap rate profile, rent levels, MSA size, industry mix.

**Always note when comps are sourced from secondary markets** — buyer cap rates and rent levels are not perfectly fungible.

## Step 5: What to Extract from Each Comp

For each comp returned, capture the following into a structured table:

### Sale Comps

| Field | Notes |
|---|---|
| Address + Submarket | Includes submarket label for context |
| Sale Date | Most recent first |
| Sale Price | $ |
| $/SF (sale) | Calculated |
| Cap Rate (in-place) | If disclosed; flag if just listing rate |
| NRSF | Subject comparison |
| Year Built / Last Reno | |
| Occupancy at sale | % |
| Tenant count / mix | Multi-tenant vs. single-tenant matters |
| Buyer Type | Institutional / private / 1031 / owner-occupier |
| Seller Type | Same |
| Notes / Anomaly Flag | E.g., "Sale-leaseback" or "Distressed" |

### Lease Comps

| Field | Notes |
|---|---|
| Address + Submarket | |
| Lease Commencement | Recent first |
| Tenant Industry | Helps identify entertainment vs. industrial vs. office |
| Suite SF | |
| Term (years) | |
| Starting $/SF (NNN basis if mixed types) | Convert MG / Gross to NNN-equivalent |
| Escalations | %/yr |
| Concessions | Free rent months, TI/LC $/SF |
| Effective $/SF (after concessions) | Critical — CoStar's headline rate ignores TI/LC |
| Renewal options | |
| Notes | Special clauses (percentage rent, expansion rights, etc.) |

**Critical for lease comps**: get **EFFECTIVE rent**, not just face rate. CoStar's listing rate is often a starting position; CompStak captures the actual signed deal including concessions. A $10/SF face with 6 months free + $20/SF TI on a 5-year deal is effectively closer to $8/SF.

## Step 6: Output — Comp Pull Spec Memo

Save to: `<vertical>/deals/<deal-slug>/comp-pull-spec-<YYYY-MM-DD>.md`

Format:

```markdown
---
deal: <Deal Name + Location>
date: YYYY-MM-DD
purpose: Specifies the optimal CoStar / CompStak filters for this deal's comp pull
target-counts:
  sale comps: 6-12
  lease comps: 10-20
---

# <Deal Name> — Comp Pull Spec

## Subject Profile

| | |
|---|---|
| Asset Type | <Industrial / Flex / etc.> |
| Market / Submarket | |
| Subject Size | XX,XXX SF |
| Subject Vintage | Built XXXX |
| Subject Occupancy | XX% |
| Subject Lease Type | NNN / MG |

## Primary Filter Spec — CoStar

**Sale Comp Filter:**
- Geographic Radius: X miles from <subject address>
- Product Type: Industrial → Flex (+ Warehouse if subject mixed)
- Size: XX,000 - XX,000 SF
- Vintage: XXXX - XXXX (subject ±10yr)
- Sale Date: Last 12 months
- Occupancy at sale: 90%+
- Building Class: B+ or higher

**Lease Comp Filter:**
- Same geographic radius
- Same product type
- Suite size: subject's largest suite ± 50%
- Lease commencement: last 18-24 months
- Lease type: NNN preferred; include MG if NNN thin

## Fallback Ladder

If primary returns < 5-8 sale comps OR < 10 lease comps:
- **Tier 1:** Widen radius 50% (X mi → X mi)
- **Tier 2:** Widen vintage band (±10yr → ±20yr)
- **Tier 3:** Lengthen time window (12mo → 24mo)
- **Tier 4:** Loosen product type (flex → flex + warehouse + light mfg)
- **Tier 5:** Secondary markets — <list 2-3 similar markets>

Document which tier the final comp set was sourced from.

## Secondary Markets (if needed)

1. <Market 1> — <why similar>
2. <Market 2> — <why similar>
3. <Market 3> — <why similar>

## What to Extract per Comp

**Sale comps:** Address, sale date, sale $, $/SF, cap rate, NRSF, vintage, occ at sale, tenant count, buyer/seller type, anomaly notes

**Lease comps:** Address, commencement, tenant industry, suite SF, term, face $/SF, escalations, concessions, **effective $/SF**, renewal options

## Validation Cross-Checks

- KB target-market-comps-database: `real-estate/wiki/target-market-comps-database.md`
- Market deep-dive: `real-estate/wiki/<market>-deep-dive.md`
- Cross-reference with CoStar market vacancy + rent trend reports for sanity check

## Anomaly Watch

Flag any comp that's:
- Sale-leaseback (cap rate skewed by tenant credit, not asset)
- Distressed / OREO sale (price below market)
- Institutional REIT exchange (1031 motivation skews price)
- Owner-occupier sale (often premium pricing)
- 1031 buyer (often premium pricing)
- Sub-50% occupied at sale (value-add takedown, different deal type)

Annotate each comp with these flags in the extraction table.

## Output Destination

Comps extracted via CoStar export → save raw to `<vertical>/deals/<deal-slug>/raw/comp-pull-<YYYY-MM-DD>.csv` (or .xlsx)

Distilled analysis → flow into `/om-deep-dive` Section 7 (Market Context) and Section 9 (Underwriting Math).
```

## Step 7: Tell User What to Do Next

After saving the spec, tell user:

1. **File path** of the spec
2. **Primary filter parameters** in one tight summary (radius, size, vintage, time window)
3. **Estimated comp universe** if known (e.g., "Cincinnati flex 50-100K SF over last 12 months — historically thin; likely Tier 2-3 fallback")
4. **CoStar action**: "Run the pull, save the export to `<path>/raw/`, and I'll distill into the analyst memo's market section"
5. **Secondary platforms**: If validation needed, suggest CompStak pull for lease rate verification

## Cross-references the skill should read

- `real-estate/wiki/target-market-comps-database.md` — Existing comp data for LFF markets
- `real-estate/wiki/flex-lease-comps-by-market.md` — Flex-segmented lease comps
- `real-estate-fund/wiki/deal-screening-criteria.md` — Market-specific $/SF and cap rate ranges
- `MEMORY.md` — Market tiering, target markets, dropped markets

## Common Pitfalls

- **Don't trust CoStar's headline lease rate.** It's the listing / face rate. Effective rates (after concessions, TI/LC) are typically 10-20% lower for a fresh lease. Use CompStak to verify.
- **Don't filter too tight on first pull.** A primary that returns 2 comps is useless. Better to start at "reasonable tight" and document the fallback as needed.
- **Don't accept stale comps without flagging.** A 2022 sale at 6.5% cap is not comparable to a 2026 environment at 7.5%; show the date.
- **Don't conflate sale-leaseback cap rates with arm's-length flex sales.** Sale-leaseback caps reflect tenant credit, not asset value.
- **Don't mix lease types without normalizing.** A $10 NNN ≠ $12 MG ≠ $14 Gross. Convert to NNN-equivalent before comparing.
- **Don't pull comps from the wrong submarket because the radius captured it.** A 10-mile radius from West Chester captures Northern Cincinnati industrial corridor — those are NOT comparable to a Liberty Township flex. Verify each comp's submarket label.
- **Don't skip the anomaly flags.** A great-looking comp at $130/SF that turns out to be a sale-leaseback to a credit tenant is a misleading benchmark.
- **Don't forget to update KB.** When the pull is done, the distilled findings should feed `target-market-comps-database.md` so the next deal in the market starts with better baselines.

## Test Example — Liberty Office Park (Cincinnati Flex)

**Subject profile:**
- 76,501 SF flex, built 2007, 100% occ, Liberty Township / West Chester submarket, B+ tilt-up
- Cincinnati MSA — potential add to target list, no prior LFF deals here

**Primary filter (would have been):**
- Radius: 7 miles from 7102 Office Park Drive (mid-density MSA)
- Product type: Industrial → Flex + Warehouse (mixed-use building)
- Size: 38,000-150,000 SF
- Vintage: 1997-2017
- Sale date: trailing 12 months
- Occ at sale: 90%+
- Class: B+ or A

**Likely outcome:** Tier 2-3 fallback in Cincinnati flex. Submarket is thin. Liberty's analyst memo noted "Cincinnati industrial overall: 7.5% average Q1 2026... flex specifically: 7.2% vacancy rate (highest among industrial types)" — pulled from CoStar market reports, not individual comps. Comp universe at that exact size + vintage in West Chester is small.

**Recommended fallback path:**
- Tier 1 (radius 10 mi) likely needed
- Tier 5 secondary markets: **Columbus + Indianapolis + Louisville** as fallback (per Section 4 table)

**For lease comps:** Tee Box at $9.50/SF NNN (10/2025) is the freshest in-building comp. Should pull 4-6 more West Chester / Tri-County leases at 5-15K SF flex to validate Tee Box as a market rate vs. a tenant-friendly outlier.

**Lesson learned:** This is the exact deal where doing the comp work right would have shortened the analyst memo. The session ran ~4 hours partly because comp validation was iterative; with this spec it's 1 hour.

## Updates / Iteration

As LFF accumulates real comps work, the **Secondary Markets** table (Section 4) and the **Size Range** defaults (Section 2) should be refined based on what's worked. Specifically:

- If Cincinnati flex consistently requires Indianapolis fallback, codify that
- If Columbus flex returns adequate primary comps without expansion, note that for future Columbus deals
- If a specific submarket within an MSA proves to have its own comp behavior (e.g., NW Cincinnati vs. East Cincinnati), document that

Update this skill's tables as the KB accumulates jurisdiction-specific learnings.
