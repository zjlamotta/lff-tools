---
name: om-screen
description: Fast 1-page go/no-go on a new deal — quick Pass / Borderline / Pursue verdict before investing in deeper analysis. Use when an OM/flyer lands or you want a snap read. For the full data scorecard (numbers, comps, sensitivity — no verdict) use `underwrite`; for an institutional IC memo use `om-deep-dive`.
---

# OM Screen — Fast Pass / Pursue Filter

A new deal is in front of the user. The goal is to filter the 70-80% of OMs that don't fit LFF criteria **before** investing 1-3 hours in a full deep-dive memo. Output is a compact 1-page memo that says one of three things: **Pass** (instant kill), **Borderline** (needs human judgment to advance), **Pursue** (run `om-deep-dive` + `broker-questions`).

## When to use this vs. other skills

- `om-screen` (this skill) → 5-10 min filter, returns a verdict (Pass/Borderline/Pursue). Decides whether to invest more time.
- `underwrite` → fast data scorecard (extract, comps, price sensitivity, return targets — no verdict). Run when you want the numbers laid out, not just a verdict.
- `om-deep-dive` → Full analyst memo (60-90 min). Only after `om-screen` returns Pursue.

## Step 1: Extract Minimal Deal Data

From the OM, flyer, or user description, capture only what's needed for screening:

- Address, city, state, market (must identify the LFF target-market label)
- Building SF + tenant count
- Asking price
- $/SF (calculate if not stated)
- In-place NOI + cap rate (calculate if not stated)
- Year built + last renovation
- Construction type / class
- Office %  (or note if unknown)
- Lease structure (NNN / MG / Gross / mixed)
- WALT (or rough lease maturity profile)
- Multi/single tenant
- Any obvious flags from the OM cover page

**Don't extract rent rolls, comps, or operating detail** — that's deep-dive scope.

## Step 2: Universal Hard Reject Check

Read `real-estate-fund/wiki/deal-screening-criteria.md` — find the **Universal Hard Rejects** section. Check the deal against ALL of these:

- Building <10,000 SF → **Pass**
- Pre-1970 construction with no documented renovation → **Pass**
- Texas outside DFW metro → **Pass**
- Condo / strata-titled industrial → **Pass**
- More than 2 stories → **Pass**
- Market outside target geographies (see [[project_target_markets]] + [[project_market_tiering]]) → **Pass**
- Office ratio >40% with no conversion path → **Pass**
- Single-tenant with 5+ year remaining lease (not value-add) → **Pass**

**If ANY hard reject hits → return Pass immediately.** Skip remaining steps. Output the verdict + which reject triggered it.

## Step 3: Market Eligibility & Tier Check

Read `MEMORY.md` to surface:
- Current target market list (Tier 1 / 2 / 3) per `project_market_tiering.md`
- Dropped markets per `project_target_markets.md`
- NJ / LI passive-only constraint per `feedback_deployment_strategy.md`

Map deal market to LFF tiering:
- **Tier 1 (deploy now)** — Columbus, KC, Tampa, Nashville, Richmond → higher conviction; more flex on price
- **Tier 2** — defer or smaller checks
- **Tier 3** — typically skip unless asset is exceptional
- **Off-list** — Pass (unless special-situation flag set)
- **NJ / LI** — passive-only; if active GP role, Pass

If deal is in a "potential add" market (Jacksonville, Cincinnati), flag for human review.

## Step 4: Market-Specific Pricing Check

In `real-estate-fund/wiki/deal-screening-criteria.md`, find the section for the deal's market. Check:

- **$/SF zone**: green (below target floor) / target / red (walk-away)
- **Going-in cap**: meets floor? (Columbus 7.0-8.5%, KC similar, etc.)
- **In-place rent**: above/below market range?
- **Building size**: in 20-80K SF sweet spot?

For Tier 1 markets where the floor/ceiling spread is published, note:
- Industrial floor rent (UW basis)
- Flex ceiling rent (stretch target)
- Spread = manufactured-equity opportunity

Per `feedback_underwriting_floor_ceiling.md`: **never aggregate floor + ceiling**; underwrite to industrial, then flag the flex spread separately.

## Step 5: Dealkiller Surface

Flag anything that materially shifts the verdict:

- **Texas reassessment risk** — If DFW deal, note that taxes must be UW at purchase price × 1.8%, not seller's basis (per `feedback_tx_reassessment.md`). $/SF on the OM probably understates true cost.
- **TIF or special tax structure** — Look for any abated taxes, special assessments, district fees. Liberty Office Park taught us TIFs can mask future tax explosion at reassessment.
- **Single-tenant + long WALT** — If WALT > 5 years and tenant occupies >40%, value-add thesis breaks.
- **Office % >30%** — Even if not >40% reject, this is execution risk.
- **Sub-class construction** — Wood frame, low clear height, no docks → walk-back from industrial designation.
- **Concentration risk** — If top tenant is >30% of revenue, surface it.
- **Lease type** — Mixed MG/Gross instead of NNN means expense risk is on owner.

## Step 6: Output 1-Page Memo

Save to `<vertical>/deals/<deal-name>/om-screen-<date>.md` (e.g., `real-estate/deals/liberty-office-park/om-screen-2026-05-14.md`).

Format:

```markdown
---
deal: <Deal Name + Location>
broker: <Brokerage>
date_screened: YYYY-MM-DD
verdict: Pass | Borderline | Pursue
related:
  - <OM source file path>
---

# <Deal Name> — OM Screen

## Verdict: **<Pass | Borderline | Pursue>**

**Two sentences why.** First sentence is the headline reason (e.g., "Asking $/SF is 23% above Columbus walk-away threshold; cap rate at 6.1% sub-floor"). Second sentence is the action (e.g., "Pass with no further work" or "Pursue — recommend `om-deep-dive` + `broker-questions` next").

## Quick Numbers

| Metric | OM Value | LFF Benchmark | Position |
|---|---|---|---|
| Asking Price | $X.XM | — | — |
| $/SF | $XXX | $XX-$XXX target | 🟢/🟡/🔴 |
| In-place Cap | X.X% | ≥X.X% floor | 🟢/🟡/🔴 |
| Building SF | XX,XXX | 20-80K target | 🟢/🟡/🔴 |
| Office % | XX% | <40% reject line | 🟢/🟡/🔴 |
| Construction | Year / Class | Post-1970 / Class B+ | 🟢/🟡/🔴 |
| WALT | X.X yrs | 2-4 yrs target | 🟢/🟡/🔴 |
| Market | <City> | Tier <#> | 🟢/🟡/🔴 |

## Hard Reject Check

- [ ] <Each universal hard reject listed; check or X>

## Pricing vs. Market (Tier 1 markets only)

- Industrial Floor: $X.XX/SF NNN (UW basis)
- Flex Ceiling: $X.XX/SF NNN (stretch target)
- Spread: $X.XX = potential manufactured equity
- In-place rent vs. floor: ___% above/below

## Dealkillers Surfaced

1. <Item 1 + severity>
2. <Item 2 + severity>

## Next Action

- **If Pursue:** invoke `om-deep-dive` for full analyst memo, then `broker-questions` to spin up tracked Q-list
- **If Borderline:** specific human-judgment item (e.g., "Cincinnati not on list but Steven has local knowledge — Zach to decide whether to advance")
- **If Pass:** log via `deal-log` skill with reason; archive OM in `<vertical>/raw/`
```

## Step 7: Recommend Next Step

End the response to the user with one clear sentence:

- **Pursue:** "Recommend running `/om-deep-dive` next, then `/broker-questions` to scaffold the Q-list."
- **Borderline:** "Borderline — flagged [specific item] for your call. If you want to advance, run `/om-deep-dive`; if not, `/deal-log` to file the pass."
- **Pass:** "Pass per [specific reject]. Run `/deal-log` to file it."

## Common Pitfalls

- **Don't underwrite the deal here.** No NOI builds, no cap rate sensitivities, no rent-roll-level analysis. That's `om-deep-dive`.
- **Don't skip the hard reject check.** Many deals look attractive on price but fail on basic geography or product type.
- **Don't forget Texas reassessment** — $/SF on the OM is almost always understated for DFW deals.
- **Don't include TIF analysis here** — flag it as a dealkiller item for deep-dive; don't try to size the reassessment risk.
- **Don't be afraid to Pass.** The point of this skill is to be ruthless. A 70-80% Pass rate is healthy.

## Test Examples

- **Liberty Office Park** (West Chester OH, $8.05M, $109/SF, 7.68% in-place cap, 73K SF flex, 5 tenants):
  - Hard rejects: none
  - Market: Cincinnati MSA (not on Tier 1; potential-add per market tiering)
  - $/SF: arguably reasonable for Cincinnati flex
  - In-place cap 7.68% above floor
  - Verdict: **Borderline** — Cincinnati not on Tier 1 list but Steven has local knowledge and asset profile fits. Recommend deeper look.
  - This matches the actual decision made 2026-05-14.

- **A hypothetical Atlanta deal** (any size, any price):
  - Atlanta dropped from target list 2026-04-09 → **Pass**, no further work.
