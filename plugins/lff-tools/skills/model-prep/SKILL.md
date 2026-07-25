---
name: model-prep
description: Bridge a screened/underwritten deal into the LFF UW Model Form — emit the deal's inputs (deal-basics + rent roll) mapped to the model's exact input cells, so the full Excel model is pre-populated instead of hand-keyed. Runs at the 3→4 handoff (after om-deep-dive, before the Excel model), so the assumptions are their most refined. Fills INPUT cells only; never the calc engine. Use when a deal has an accepted offer / is going to full model. Output feeds straight into the Codex gut-check.
---

# Model Prep — deal → LFF UW Model Form handoff

You have a deal that's cleared the funnel (offer out / accepted) and is going into the full Excel model.
This skill collapses the 2-hour data-entry step by emitting the deal's inputs mapped to the **LFF UW Model
Form**'s exact input cells. The model's engine (Monthly CF, Annual CF, Waterfall Returns, Forward Curves) is
100% formula-driven — **you never touch it.** You only fill the hardcoded input cells.

## Hard rules (read first)

- **NEVER openpyxl-save the model form.** It has ~11k formulas, charts, and data-validation that openpyxl
  silently drops (same rule as the 4689H workbook). Either (a) output the mapped block for the user to
  enter, or (b) write via **Claude-in-Excel** into a **copy** of the form — live, preserving everything.
- **Fill INPUT cells only** (the ones listed below). Leave every formula/calculated cell alone.
- **Work on a copy**, named `{deal-slug} UW Model v{N}.xlsx` — never the blank master form.
- v1 scope = **deal-basics + rent roll** (that's the time sink). Opex / property taxes / financing / capex
  are phase 2 — flag them as "still to enter by hand" so nothing is silently skipped.

## Input schema — LFF UW Model Form (pinned to v8; re-map if the form version bumps and moves cells)

### A) Summary tab — deal basics (individual cells)

| Cell | Input | From the deal |
|------|-------|---------------|
| `Summary!E8`  | Contract date | LOI/PSA date |
| `Summary!E9`  | Due-diligence period (days) | LOI terms (default 45) |
| `Summary!E10` | Closing period (days) | LOI terms (default 60) |
| `Summary!E12` | **Acquisition price** | the accepted/offer price |
| `Summary!E16` | **Seller stated NOI** | OM / T-12 |
| `Summary!H17` | Working capital | LFF standard (default $1,000,000 unless deal-specific) |
| `Summary!I21` | Stabilization period (months) | value-add plan (default 60) |

Do NOT set Building Size (`E7`) or Cap Rate (`E17`) — both are formulas (`E7='Rent Roll'!G57` sums the rent
roll GSF; `E17=E16/E12`). They compute once the rent roll is in. Leave promote/fee assumptions
(`M7` pref, `L9` GP%, fee block) at the form's LFF defaults unless the deal has bespoke JV terms.

### B) Rent Roll tab — one row per suite/tenant, starting row 7 (fill these columns only)

| Col | Input | Notes |
|-----|-------|-------|
| `C` | Suite / unit label | e.g. "UBC I 190" |
| `E` | Tenant name | |
| `F` | Actual lease expiration (date) | drives remaining-term calc |
| `G` | GSF | gross SF |
| `H` | Loss factor | default `0` (GSF = RSF) |
| `L` | Lease term (months) | |
| `Q` | Base rent PSF (annual $/SF) | |
| `R` | Additional rent (optional) | reimbursements if modeled here; else blank |
| `S` | Annual increase / escalation | as decimal, e.g. `0.03` for 3%/yr (default `0` if flat) |

Leave calculated columns alone: `D` Check, `I` RSF, `J` %GLA, `K` Lease Start (`=Summary!E11`), `M` Remaining
Term, `N` Lease Exp, `O` Annual Rent, `P` Monthly Rent. One suite per row; keep them contiguous from row 7.

## Process

1. Pull the deal's rent roll + basics from the `underwrite` scorecard / `om-deep-dive` memo (or read the OM +
   rent roll directly). Reuse the already-extracted structure — don't re-key from scratch.
2. Map every value to the cells above. Where a value is missing, mark it `⟨NEEDS INPUT⟩` — never guess a
   rent, size, or expiration.
3. **Output** (v1 — paste-ready block):

```
═══ MODEL PREP: {Deal} → LFF UW Model Form ═══
Copy the blank form to:  {deal-slug} UW Model v1.xlsx  (never edit the master)

SUMMARY (enter each cell)
  E8  Contract date        = {date}
  E9  DD (days)            = {45}
  E10 Closing (days)       = {60}
  E12 Acquisition price    = {price}
  E16 Seller NOI           = {noi}
  H17 Working capital      = {1,000,000}
  I21 Stabilization (mo)   = {60}

RENT ROLL (paste from row 7; columns C,E,F,G,H,L,Q,R,S)
  | Row | C Suite | E Tenant | F Exp | G GSF | H Loss | L Term(mo) | Q Rent PSF | R Addtl | S Escal |
  | 7   | ...     | ...      | ...   | ...   | 0      | ...        | ...        |         | 0.03    |
  ...

STILL TO ENTER BY HAND (v1 scope stops here):
  - Operating Expenses tab · Property Taxes tab · Financing Scenarios tab · CapEx tab
  (flag anything deal-specific you already know, e.g. reassessed tax, debt quote)
═══
```

4. Tell the user: which inputs are filled, which are `⟨NEEDS INPUT⟩`, and that opex/tax/financing/capex are
   still manual (v1). Recommend running the **Codex gut-check** (`_meta/prompts/deal-analysis-codex-gutcheck-prompt.md`)
   on the assumptions before trusting the model output.

## Upgrade path (not v1)
Direct-write into the copy via **Claude-in-Excel** (live app, preserves formulas/charts/validation) instead
of a paste block — same cell map. Add opex/tax/financing/capex mapping as v2.
