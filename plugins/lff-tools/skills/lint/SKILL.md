---
name: lint
description: Run a health check on the knowledge base wiki. Finds data conflicts, broken links, missing backlinks, and coverage gaps.
---

# Lint Health Check

Run the WORKFLOW.md Section 4 health check. 

## Arguments
- `$ARGUMENTS` — specific vertical (e.g., "real-estate") or "all" for every vertical. Default: all.

## For each vertical:
1. Read `_index.md` and every article listed in it
2. Flag any inconsistent data points (conflicting numbers, dates, claims across articles)
3. Flag any broken wikilinks (`[[something]]` pointing to non-existent articles)
4. Suggest 5 new article candidates based on gaps in coverage
5. Suggest 3 cross-vertical connections that aren't yet documented
6. Check all backlinks are bidirectional (if A links to B, does B link back to A?)
7. Check date_updated fields — flag any that look stale

Write findings to `{vertical}/outputs/lint-{today's date}.md`.

If "all" is specified, also check `_cross-vertical.md` in the repo root for completeness.
