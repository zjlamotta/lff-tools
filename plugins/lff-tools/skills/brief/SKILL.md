---
name: brief
description: Monday morning briefing. Surfaces active deals, pipeline status, LP interactions in flight, stale data, and weekly priorities. Run at start of week.
---

# Weekly Briefing

Generate a situational awareness briefing for Zach at the start of the week. Output directly in the chat (don't save to file — the user will decide what to do with it).

## Sections

### 1. Active Deal Pipeline
- Check `/mnt/c/Users/zjlam/Downloads/` for any OMs or deal flyers downloaded in the last 7 days that haven't been analyzed
- Read recent entries in `real-estate-fund/deal-log/` for deals under active consideration
- Flag any deals where follow-up is overdue (LOI sent but no response in 10+ days, DD in progress for 30+ days, etc.)
- List deals by status: Active (under LOI/DD), Evaluating (screening), Passed (last 7 days)

### 2. LP Pipeline
- Read `real-estate-fund/lp-log/` entries
- Flag LPs with pending follow-ups or questions
- Identify commitment signals that need a next step (warm LPs going cold)
- Highlight recurring objections that might indicate a pitch adjustment needed

### 3. Knowledge Base Freshness
- Check date_updated fields on market articles
- Flag any target market with no update in 30+ days (especially Columbus, KC, Tampa, Nashville, Richmond — priority markets)
- Flag any downloaded PDFs in `raw/` from the past week that haven't been compiled

### 4. Open Items From Memory
- Read `~/.claude/projects/-home-zjlammotta-projects-knowledge-base/memory/MEMORY.md` and surface any open items, deferred decisions, or things Zach asked to revisit

### 5. This Week's Priorities (Suggested)
Based on everything above, recommend 3-5 priorities for the week. Be specific:
- Not "follow up on deals" but "Send revised LOI to Kessinger Hunter on 6850 W 47th Terrace at $4.5M"
- Not "refresh markets" but "Refresh Nashville market data — last update was 2026-04-10, Q1 2026 reports now available"

### 6. Questions to Ask Zach
End with 2-3 questions that only Zach can answer but that would unblock progress. Examples:
- "Are you still pursuing the Long Island first-close deal? It's been 4 days since we last discussed."
- "The Center Capital follow-up — did that call happen?"
- "Has Paul been briefed on the key-man provision structure yet?"

## Format

Keep it tight — this is a briefing, not a report. Use headers, bullets, and tables. Total output under 800 words. Bold the priorities.
