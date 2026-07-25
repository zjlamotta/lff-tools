---
name: lp-log
description: Log an LP interaction (call, meeting, email exchange). Tracks commitment signals, objections, follow-ups. Builds an IR CRM over time.
---

# LP Interaction Log

Create an LP log entry after any substantive LP interaction.

## Arguments
`$ARGUMENTS` should include LP name or firm. If not provided, ask Zach.

## Ask Zach for:

1. **LP identity:** Name, firm, relationship (warm intro, cold, referral from whom)
2. **Type of interaction:** First call, follow-up, meeting, email exchange, due diligence
3. **What they pushed on:** Objections, concerns, questions you had to handle
4. **Commitment signal (1-5):** 
   - 1 = cold / passing / no interest
   - 2 = polite listener, no traction
   - 3 = interested but not urgent
   - 4 = warm, engaged, doing DD
   - 5 = verbal commitment / soft circle
5. **What they said they'd do next** and by when
6. **What you said you'd do next** and by when
7. **Anything that surprised you**

## Write to `real-estate-fund/lp-log/{date}-{lp-slug}.md`

```markdown
---
date: YYYY-MM-DD
lp_name: {Name or Firm}
firm: 
relationship: {warm intro | cold | referral from X}
interaction_type: {first call | follow-up | meeting | email | DD}
signal: {1-5}
tags: [lp-log, {warm|cold|active|dormant}]
---

# {LP Name / Firm} — {Date}

## Who They Are
{Brief context — type of capital, check size range, prior investments, relevant decision makers}

## The Interaction
{What happened — call, meeting, email thread. Keep factual.}

## What They Pushed On
- 
- 

## What Landed
{What parts of the pitch or conversation resonated}

## Commitment Signal: {1-5}
{One-line explanation of the rating}

## Next Steps

**They will:**
- 

**I will:**
- 

## Surprises / Tells
{Anything unexpected — tone shifts, specific language, comparisons they made}

## Pattern Match
{Similar to any other LP in the log? Same objection as someone else?}
```

## After Saving

1. **Check the LP log directory for recurring objections.** If the same objection is appearing across 3+ LPs, flag this to Zach — it means the pitch has a weak spot that needs addressing.
2. **Flag any LPs that have been "warm but not progressing" for 30+ days.** Those need a specific next step, not another "just checking in."
3. **Report what you did and any patterns noticed.**
