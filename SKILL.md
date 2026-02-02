---
name: smart-screenshot-qa
description: Efficient frontend QA using the right verification method. Use when doing browser-based QA, taking screenshots, verifying UI changes, or when screenshot loops are wasting tokens. Optimizes between DOM inspection, targeted zoom, and full screenshots.
---

# Smart Screenshot QA

Stop the screenshot spirals. Choose the right verification method for the job.

## Token Costs (Tested on GitHub.com)

| Method | Tokens | Best For |
|--------|--------|----------|
| Targeted zoom (region) | 100-200 | Component styling, specific elements |
| `find` (natural language) | 500-1,000 | "Is there a submit button?" without full DOM |
| `read_page` (filter: "interactive") | 800-2,000 | Structural checks, element existence |
| Full viewport screenshot | 1,000-1,500 | Layout verification, final sign-off |
| `read_page` (full DOM) | 6,000-25,000+ | Avoid on complex pages |

**Key insight:** Targeted zoom is 5-10x cheaper than full screenshots. Filtered DOM is often cheaper AND faster than screenshots for structural questions. Full DOM can cost 15-20x more than a screenshot.

## Decision Tree

**"Does element X exist / have correct text / attributes?"**
→ `read_page` with `filter: "interactive"`. Cheaper and faster than screenshots.

**"Does this button/component look right?"**
→ `zoom` on that specific region. 5-10x cheaper than full screenshot.

**"Is the layout/spacing/alignment correct?"**
→ Single full screenshot, but only after batching changes.

**"Final check before shipping"**
→ One full screenshot. Done. Move on.

## Anti-Patterns (The Real Token Killers)

1. **Screenshot loops** - Taking the same screenshot over and over "to make sure." One verification that passes = done.
2. **Full screenshot for one component** - Use targeted zoom. 5-10x cheaper.
3. **Screenshot after every change** - Batch 3-5 changes, then one screenshot.
4. **Retaking screenshots you already have** - Reference existing imageId if nothing visual changed.
5. **Full DOM dump on complex pages** - Always use `filter: "interactive"` (60-80% savings).
6. **Full-page scroll screenshots** - Use zoom for specific sections.

## Why Loops Are The Real Problem

Individual screenshots aren't that expensive (700-1,500 tokens). The problem is spirals:

```
change → screenshot → tweak → screenshot → "let me check" → screenshot → "one more" → screenshot
```

5 unnecessary screenshots = 3,500-7,500 wasted tokens. Per component. Per session.

## Before/After Comparisons

You cannot retroactively view old imageIds. They persist for the browser session but disappear from context when it gets summarized.

Before taking a "before" screenshot:
- Note key visual details in text (spacing, colors, positions)
- Make changes
- Take "after" screenshot and compare against your notes

If you need to reference a screenshot later in a long session, note the key details in text immediately after taking it.

## Exit Criteria (When QA is Done)

Stop when:
- The specific visual or structural property requested has been verified
- No relevant console errors (`read_console_messages`)
- Interactive elements respond correctly (if applicable)

Do NOT keep screenshotting to make it "perfect" or to "double-check."

## Quick Reference

```
"Is there a [thing]?"  → find (natural language, mid-cost)
Element exists?        → read_page filter:"interactive" (cheapest for structural)
Text/attribute check   → read_page filter:"interactive"
Component styling      → zoom (5-10x cheaper than full screenshot)
Layout verification    → single screenshot after batching
Final sign-off         → one screenshot, then stop
Full DOM               → avoid (can cost 15-20x more than screenshot)
```
