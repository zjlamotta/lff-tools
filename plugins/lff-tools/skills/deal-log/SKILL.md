---
name: deal-log
description: Log a deal decision (passed, pursuing, closed, etc.) to the deal log. Creates a structured post-mortem entry for future pattern matching.
---

# Deal Log Entry

Create a new deal log entry in `real-estate-fund/deal-log/`.

## Arguments
`$ARGUMENTS` should include the deal identifier (e.g., "Fort Mill 330-344 Springhill Farm — PASSED" or "6850 W 47th Terrace Mission KS — COUNTERED"). If not provided, ask Zach.

## Process

1. **Ask Zach for the key details if not in the conversation:**
   - Deal address/identifier
   - Status: Evaluating | Counter Sent | LOI | Under Contract | Closed | Passed | Dead
   - Asking price and $/SF
   - Key facts (size, cap rate, rent roll, condition)
   - His decision and WHY (the why is the most important part)
   - What would change the decision

2. **Pull context from this conversation or recent analysis if available.** If `/underwrite` or `/om-deep-dive` was run on this deal, use that analysis as the foundation.

3. **Write the log entry to `real-estate-fund/deal-log/{date}-{address-slug}.md`** using this template:

```markdown
---
date_logged: YYYY-MM-DD
deal: {address}
market: {city, state}
status: {Evaluating | Counter | LOI | UC | Closed | Passed | Dead}
asking_price: ${X}M
price_psf: ${X}
sf: {X}
tags: [deal-log, {market-tag}, {status-tag}]
---

# {Address} — {Status}

## Deal Snapshot

| | |
|---|---|
| Address | |
| Market | |
| Building SF | |
| Asking price | |
| $/SF | |
| Cap rate | |
| Occupancy | |
| Broker | |

## The Decision

**Status:** {Passed / Pursuing / etc.}

**Why:**

{Zach's reasoning in his own words — this is the most important section}

**What would change the decision:**

{What would have to be true for a different outcome — lower price, better in-place rents, different market conditions, etc.}

## Key Facts

{Bullet points of the material facts}

## Comp Benchmark

{How this deal compared to market — $/SF, cap rate, rent vs avg}

## Red Flags / Positives

**Red flags:**
- 

**Positives:**
- 

## Follow-up

{Any next steps — counter offer, keep broker warm, revisit if price drops, etc.}

## Related

- Broker: {name, firm}
- PM candidate for this market: {from property-management-vendor-list}
- Similar deals in log: {link any that pattern-match}
```

4. **Report back:** Confirm the file was created and where it lives. Ask if there's anything else to log about this deal.
