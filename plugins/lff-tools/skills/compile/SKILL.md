---
name: compile
description: Compile new raw sources into the knowledge base wiki. Use when new files have been added to a vertical's raw/ folder.
---

# Compile New Sources

The user wants to compile new/updated raw files into the wiki. Follow the WORKFLOW.md compilation process:

## Arguments
- `$ARGUMENTS` — the vertical to compile (e.g., "real-estate", "real-estate-fund", "data-centers", "energy"). If not specified, check all verticals for new files.

## Process

1. **Identify new files:** Compare files in `{vertical}/raw/` against `{vertical}/raw/_sources.md`. Any file not listed in _sources.md is new.

2. **For each new source:**
   - Add it to `raw/_sources.md` with date, title, type, tags, and 2-line summary
   - If PDF: extract with `pdftotext` and assess content quality
   - If XLSX: parse with Python zipfile/xml approach
   - If DOCX: parse with Python zipfile/xml approach
   - If MD: read directly

3. **Extract and compile:**
   - Extract key concepts, data points, and facts
   - Either update existing wiki articles or create new ones in `{vertical}/wiki/`
   - Maintain YAML frontmatter (title, date_created, date_updated, tags, sources)
   - Add backlinks section at bottom of new articles

4. **Update indexes:**
   - Update `{vertical}/wiki/_index.md` with new article entries
   - Update `{vertical}/wiki/_concepts.md` if new concepts introduced
   - Check for cross-vertical connections → update `_cross-vertical.md` if found

5. **Report:** Summarize what was compiled — files processed, articles created/updated, any issues found.
