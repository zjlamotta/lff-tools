---
name: broker-questions
description: Scaffold a tracked broker question list for a deal. Use when a deal has cleared `om-screen` and is being actively pursued, OR when user explicitly asks to "spin up the broker Q list" / "what do we need to ask the broker." Output is a markdown file with 7 universal categories + 1 deal-specific category, traffic-light status tracking, and deal-specific questions surfaced from OM gaps.
---

# Broker Questions — Tracked Running List

Every deal that advances past `om-screen` generates a list of questions for the broker. This skill scaffolds that list in a consistent, trackable format so questions don't get lost between calls, emails, and follow-ups.

## When to use this skill

- Right after `om-screen` returns Pursue and/or `om-deep-dive` is being built
- When the user says "what should we ask the broker," "spin up the Q list," "broker followup," or similar
- When questions are accumulating in chat or email and need to be consolidated

## Step 1: Identify Deal + File Path

From context, determine:
- **Deal name** (e.g., "Liberty Office Park" → slug `liberty-office-park`)
- **Vertical** (real-estate, marinas, data-centers, etc.)
- **Broker(s)** (firm + named individuals if known)

Default file path: `<vertical>/deals/<deal-slug>/broker-questions-running-list.md`

If `<vertical>/deals/<deal-slug>/` directory doesn't exist, create it.

## Step 2: Scaffold the File with Standard Structure

Use this exact template (adapted from `real-estate/deals/liberty-office-park/broker-questions-running-list.md` — the canonical reference):

```markdown
---
deal: <Deal Name + Location>
broker: <Brokerage — Named contacts if known>
date_started: YYYY-MM-DD
status: Active — running list, add to as questions emerge
---

# <Deal Name> — Running Broker Questions

Compiled questions for <Broker> during diligence. Mark each item as it gets answered.

---

## Status Legend

- 🟢 = Asked, answer received
- 🟡 = Asked, awaiting answer
- 🔴 = Not yet asked

---

## 1. Model / Underwriting

| # | Status | Question |
|---|---|---|
| 1.1 | 🔴 | **Argus model Excel exports** — Cash Flow, Tenant Rent Roll, Lease Expiration Schedule, Property Resale Analysis, Investment Returns Summary, Operating Expense Detail, Assumptions Report |
| 1.2 | 🔴 | **Sale comp support** — What 3-5 comp transactions did the brokerage underwrite to support the cap rate / $/SF? |
| 1.3 | 🔴 | **Recent leasing comps in submarket** — Past 12 months, similar product type and size |
<!-- Add deal-specific items below -->

---

## 2. Tenant Credit & Lease Terms

| # | Status | Question |
|---|---|---|
| 2.1 | 🔴 | **Personal guarantor confirmation across all leases** — For each tenant, confirm PG, LOC, security deposit, parent/franchisor guarantees, indemnification agreements. Flag tenants with NO security mechanism. |
| 2.2 | 🔴 | **Tenant financials / D&B** — 3 years of P&Ls or D&B reports for top 3 tenants by revenue (especially any without PG) |
| 2.3 | 🔴 | **Lease abstracts vs. full leases** — Reconcile any discrepancies; obtain full leases for top tenants |
| 2.4 | 🔴 | **Renewal intent on near-term expirations** — Any conversations with tenants whose leases expire within hold period? |
<!-- Add deal-specific items -->

---

## 3. Property Physical / CapEx

| # | Status | Question |
|---|---|---|
| 3.1 | 🔴 | **Roof condition** — Age, last inspection, maintenance records, estimated remaining useful life, replacement budget |
| 3.2 | 🔴 | **HVAC condition** — Age, service records, near-term replacements |
| 3.3 | 🔴 | **Parking lot** — Asphalt history, sealcoating cycle, remaining life |
| 3.4 | 🔴 | **Phase I Environmental** — Most recent Phase I; historical property use; any recognized environmental conditions |
| 3.5 | 🔴 | **Property Condition Assessment (PCA)** — Most recent PCA if available |
<!-- Add deal-specific items: bulkheads (marina), specialty equipment, etc. -->

---

## 4. Title / Survey / Zoning

| # | Status | Question |
|---|---|---|
| 4.1 | 🔴 | **Easements / encumbrances** — Any easements, restrictions, deed conditions affecting use or future development |
| 4.2 | 🔴 | **Zoning specifics** — Current zoning + any pending change applications; conforming/non-conforming status; restrictions on re-tenanting or change-of-use |
| 4.3 | 🔴 | **Recent survey** — ALTA survey if available |
| 4.4 | 🔴 | **Title commitment / preliminary title report** — Any encumbrances beyond standard |
<!-- Add deal-specific: waterfront permits, FAA height restrictions, etc. -->

---

## 5. Financials Detail

| # | Status | Question |
|---|---|---|
| 5.1 | 🔴 | **Historical operating expense detail** — 3 years of audited P&L by line item; reconciliation to Year 1 pro forma |
| 5.2 | 🔴 | **Historical occupancy detail** — Year-by-year occupancy; which vacancies occurred, when, how long; time-to-lease metrics |
| 5.3 | 🔴 | **CAM reconciliation history** — Past 2 years of CAM recon statements; any disputes or audits |
| 5.4 | 🔴 | **Tenant collection history** — Late pays, AR aging, defaults / cures in past 24 months |
| 5.5 | 🔴 | **Property tax assessment** — Current assessed value vs. fair market value gap; pending appeals; reassessment risk on sale (especially TX, see [[feedback_tx_reassessment]]) |
| 5.6 | 🔴 | **TIF / special assessments** — Any tax increment financing, abatement, special district fees? When do they expire? What is the reassessment trigger? |
<!-- Add deal-specific items -->

---

## 6. Market / Submarket / Demising

| # | Status | Question |
|---|---|---|
| 6.1 | 🔴 | **Demising large suites** — If single tenants occupy >25% of building, what would it take to demise into smaller bays? Architect studies, permitting requirements, cost estimates |
| 6.2 | 🔴 | **Re-tenanting velocity in submarket** — Current market time for similar-size space in this submarket; active inquiry pipeline |
| 6.3 | 🔴 | **Submarket comp activity** — Recent leases signed in past 6-12 months at comparable assets |
<!-- Add deal-specific items -->

---

## 7. Transaction Process

| # | Status | Question |
|---|---|---|
| 7.1 | 🔴 | **Seller motivation** — Why selling now, ownership history, prior offers received, time on market |
| 7.2 | 🔴 | **Marketing timeline** — Call-for-offers date, best-and-final structure, how many qualified principals receiving OM |
| 7.3 | 🔴 | **Competing offers** — Active LOIs in hand? At what level? (broker may not disclose) |
| 7.4 | 🔴 | **Earnest money expectations** — Standard for this deal type |
| 7.5 | 🔴 | **Due diligence period expectations** — Standard 30/60/90? |
| 7.6 | 🔴 | **Prior failed transactions** — Any deal that went under contract previously and fell out? Why? |
<!-- Add deal-specific items -->

---

## 8. <Deal-Specific Category — Submarket Growth / Asset-Specific>

<!-- Use this section for the deal's unique angle. Examples:
- Real estate development pipeline near subject
- Tenant industry exposure (entertainment-heavy → resilience)
- Bulkhead / waterfront engineering (marinas)
- Power capacity / utility coordination (data centers)
- Other deal-specific themes that don't fit in 1-7
-->

| # | Status | Question |
|---|---|---|
| 8.1 | 🔴 | <Deal-specific question 1> |
| 8.2 | 🔴 | <Deal-specific question 2> |

---

## To Be Added (placeholder for ongoing questions)

| # | Status | Question |
|---|---|---|
| 9.x | 🔴 | _(Zach to add as questions emerge)_ |

---

## Notes / Working Context

- Related: <link to om-screen output, om-deep-dive memo, OM source>
- <Sponsor / partner context if applicable, e.g., "Steven owns nearby and has local color">
- <Underwriting basis: pricing benchmark, equity required, fund / SPV vehicle>
```

## Step 3: Surface Deal-Specific Questions

Read the OM (or its summary in the `om-screen` output / `om-deep-dive` memo if those exist for this deal). For each universal category, look at what the OM provides vs. what's missing:

- **Section 1 (Model):** Does the OM include an Argus output? If yes, what's missing (sensitivity, return distribution)? If no, add as Question 1.1.
- **Section 2 (Tenant Credit):** Which tenants are missing financial detail? Which have NO PG and need to be flagged? Surface as specific questions naming each tenant by name.
- **Section 3 (Physical):** Any deal-specific systems unique to asset type? (Bulkhead for marinas, transformers for data centers, fuel tanks for industrial.) Surface in Section 3.
- **Section 4 (Title/Zoning):** Any unusual zoning (B-PUD, special district, waterfront)? Surface specifics.
- **Section 5 (Financials):** Note specific OpEx line items that look anomalous (TIF, ground lease, abatement).
- **Section 6 (Market):** If asset has large single tenants, ask about demising. If submarket is tight or loose, ask about velocity.
- **Section 7 (Transaction):** If the OM hints at prior failed contract or unusual sale motivation, ask directly.
- **Section 8 (Deal-Specific):** What is unique about THIS deal that doesn't fit categories 1-7? Make that the section heading.

Make questions **specific and unambiguous** — not "what's the tenant credit?" but "Get Air West Chester LLC — 3 years P&Ls or D&B report, largest tenant at 33% revenue with no PG."

Aim for **3-7 questions per category**, weighted toward the categories where the OM is thinnest.

## Step 4: Save File + Notify User

Save to the path identified in Step 1. Tell the user:

- Where the file was saved
- How many questions across how many sections
- Which categories have the most uncertainty (i.e., where the OM is weakest)
- A reminder that 8.x is the deal-specific category — invite them to tell you what the deal's unique angle is if it's not obvious

## Common Pitfalls

- **Don't paste generic placeholder questions and call it done.** The deal-specific surfacing in Step 3 is the actual value-add. A generic-only Q list is worse than no list — it creates false confidence.
- **Don't combine multiple questions in one row.** "Tenant credit AND lease terms AND personal guarantors" → break into 3 separate questions.
- **Don't skip the 8.x deal-specific section.** Every deal has at least one unique angle. If you can't identify it, ask the user.
- **Don't use color emoji for status if the user prefers plain text.** Stick with 🟢🟡🔴 per Liberty pattern; user has signaled comfort with this format.
- **Don't forget the "Working Context" footer.** That's where partners, sponsor context, and underwriting basis live.

## Test Example

- **Liberty Office Park** (`real-estate/deals/liberty-office-park/broker-questions-running-list.md` — already exists; this skill should regenerate something very similar):
  - 8 sections total (7 universal + 1 deal-specific = "Liberty Center / Submarket Growth")
  - 33 questions across all sections
  - Heavy weighting on Sections 2 (5 tenant-credit questions, especially Get Air no-PG + CRAV percentage rent + BCESC renewal)
  - Section 8 deal-specific theme = Liberty Center mall expansion and Voice of America Park area construction
  - This file is the canonical pattern to match.

## Updates / Iteration

This file is a **running list** — never delete questions. When a question is answered, change 🔴 → 🟢 and append the answer below the question (or in a date-stamped notes section). The history of asked/answered is itself valuable diligence record.

When the user says "broker answered Q 2.2," update the status in place; don't create a new file.
