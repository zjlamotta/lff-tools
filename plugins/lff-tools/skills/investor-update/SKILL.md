---
name: investor-update
description: Draft a quarterly LP investor update for an LFF syndication asset (4689H Columbus, 130R Huntington, 1165S Medford, or a new asset) from the quarter's operating financials + the prior-quarter update as the format template. Produces the update in that property's established structure — Key Updates/Summary, distributions ledger, leasing/rent roll, financing, market overview with cited sources, forward outlook — then renders a formatted .docx for review. External-doc discipline: confident body, no internal hedging. Use when quarterly financials are in and it's time to write the LP update. Lender updates (Mavik/UBS) are a separate deliverable.
---

# Investor Update — quarterly LP report

Produce a Paul-and-LP-ready quarterly update for a syndication asset. Match the property's established
format (each has a house structure), ground every number in the quarter's actuals, and render a clean
`.docx` for review. This is an **external** document — see the discipline rules below.

## Inputs

- **Property + quarter** (e.g. "4689H Q2 2026").
- **The quarter's operating financials** — entity P&L / operating spreadsheet / FS Financial Summary Form
  (rent roll, NOI, free cash flow, distribution, reserves as of quarter-end).
- **The prior-quarter FINAL update** — the format template AND the base for the cumulative distribution
  ledger. Find it in `syndications/investor-updates/`.

## Process

1. **Identify the property and read its prior-quarter FINAL** from `syndications/investor-updates/`
   (`2026-q1-{property}-*FINAL*.md` or the latest). This is the format template and the distribution-ledger
   base — you append this quarter, you don't rebuild.
2. **Extract this quarter's numbers** from the operating financials (checklist below). Cite each to source.
   Never guess a distribution, NOI, or rent-roll figure — if it's not in the financials, ask.
3. **Write the sections in this property's format** (structures below). Carry forward stable content
   (financing terms, capitalization, tenant roster) from the prior update; update only what changed.
4. **Refresh the Market Overview for the quarter** — pull current-quarter submarket data (vacancy,
   rents, absorption, notable sales) and cite the source + quarter (CBRE / JLL / Cushman & Wakefield /
   Newmark, "Q2 2026"). If no fresh third-party report is available, carry the prior quarter's themes
   forward, update the figures you can confirm, and say the metro figures are current with a one-line
   source note — do NOT invent submarket stats.
5. **Apply external-doc discipline** (below).
6. **Save the markdown** to `syndications/investor-updates/2026-q{N}-{property}-draft.md`, then **render a
   formatted `.docx`** for review (rendering note below). Never leave the update only in chat.

## Canonical structure (property house-styles)

All three open with a header (**Property name/address · "Q{N} {YYYY} Investor Update"** · a one-line sponsor
intro) and close with a warm thank-you + sign-off (**LFF Industrial** / the Sponsor). Between:

- **4689H Columbus (Hilton Business Park):** bulleted **Key Updates** → **Distributions** (amount +
  annualized CoC + contributions/distributions-to-date table) → **Financial Summary** (Q/YTD operating
  cash-flow table: revenue, expenses, NOI, debt service, FCF; reserve cash flow; cash position) →
  **Market Overview** → **Operations and Property Management** → **Financing** (term table) →
  **Leasing & Rent Roll** (2027-roll management) → **Capital Improvements** → **Forward Outlook**.
- **130R Huntington:** opening summary paragraphs (distribution + CoC, **Sale Process**, market feedback,
  occupancy) → **Sale Process Update** → **Leasing & Tenancy** (rent roll) → **Solar Operator** →
  **Capitalization** (equity/loan, principal paid, balance, rate-reset note) → **Cash Position &
  Distributions** (full ledger table) → **Market Overview** (Western Suffolk).
- **1165S Medford:** **Summary** → **Distributions** (table) → **Rooftop Solar Update** → **Operations and
  Property Management** → **Financing** (UBS term table) → **Market Overview** (Central Suffolk) →
  **Leasing** (rent roll table) → **Forward Outlook** → sign-off.

For a **new asset**, use the 1165S structure as the default and adapt.

## Source-data checklist (pull every quarter, cite to source)

- **Distribution** this quarter (and the split by member if reported) + annualized cash-on-cash on
  committed/initial equity, and how it compares to the pro-forma / prior quarter.
- **NOI** — quarter and YTD; vs budget if available.
- **Free cash flow** after debt service, AM fees, and below-NOI items.
- **Cumulative distributions + principal paid + % of equity returned** (for the ledger row).
- **Rent roll** — every tenant: SF, $/SF + lease type, expiration, status/escalation.
- **Financing** — loan balance, principal paid to date, rate, maturity (carry the term table forward).
- **Reserves / cash position** at quarter-end.
- **Events** — leases executed/renewed, sale process, solar, tax grievance, capex, roll management.

## Distribution ledger

Cumulative — copy the prior-quarter table and **append this quarter's row** (period, remaining/committed
equity, principal, cash distribution, %'s, cumulative). Keep the exact column set the property already uses
(130R's is the detailed 8-column version; 1165S's is the simple 4-column version). Update the Totals row.

## External-doc discipline (this is LP-facing)

- **Confident factual body.** No internal hedging, no `[PLACEHOLDER]`, no "verify" or "confirm" notes, no
  bracketed uncertainty. Cut an unconfirmed claim rather than bracket it. (Ref: `feedback_external_marketing_docs`.)
- Put any necessary caveat in **one** clean sentence (e.g. the market-data source note), not scattered hedges.
- Plain, warm, professional. Explain, don't spin. If a quarter was soft (a trough, a one-time item),
  state it plainly and give the reason and the forward path — that reads as competence, not weakness.

## Rendering the .docx

Render the finalized markdown to a formatted Word doc for review — clean title, property header, bold
section headers, formatted tables. In **Cowork**, use the built-in `docx` skill. From **Claude Code**, use
`python-docx` (write a `.docx`; do NOT rely on markdown alone for the deliverable). Save the `.docx`
alongside the markdown in `syndications/investor-updates/`. Keep the markdown as the editable source of truth.

## Rules

- Ground every number in the quarter's actuals — never carry a stale figure forward as if current.
- Carry forward stable prose (financing, capitalization, tenant background) verbatim; refresh only the
  numbers and the quarter's events.
- This is the **investor** update. The Mavik (4689H) and UBS (1165S) **lender** updates are separate
  deliverables — flag if one is also due, don't merge them in.
- Do NOT git commit — Claude Code is the sole committer. Save the draft + .docx to the folder for review.
- After producing the draft, offer Zach the formatted `.docx` to review before it's finalized/sent.
