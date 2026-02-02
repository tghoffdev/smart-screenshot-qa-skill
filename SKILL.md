---
name: smart-screenshot-qa
description: Efficient frontend QA using the right verification method. Use when doing browser-based QA, taking screenshots, verifying UI changes, or when screenshot loops are wasting tokens. Optimizes between DOM inspection, targeted zoom, and full screenshots.
---

# Smart Screenshot QA

Choose the cheapest verification method that answers the question.

## Token Costs (Know Your Budget)

| Method | Tokens | Use for |
|--------|--------|---------|
| `read_page` (filter: "interactive") | Low | Element exists, attributes, structure |
| `read_page` (full DOM) | HIGH (up to 12.5k) | Avoid unless necessary |
| `zoom` (region screenshot) | Low (100-200) | Specific component styling |
| Full screenshot | Medium (700-1500) | Final visual verification |

## Decision Tree

**"Does element X exist / have correct text / have correct attributes?"**
→ `read_page` with `filter: "interactive"`. No screenshot needed.

**"Does this button/component look right?"**
→ `zoom` on that specific region. Don't screenshot the whole page.

**"Is the layout/spacing/alignment correct?"**
→ Screenshot, but only once after batching changes.

**"Final check before shipping"**
→ One full screenshot. Done.

## Anti-Patterns (Stop Doing These)

1. **Screenshot after every change** - Batch 3-5 changes, then one screenshot
2. **Full page scroll screenshots** - Use zoom for specific sections
3. **Screenshot to check if element exists** - DOM is faster and cheaper
4. **Retaking screenshots you already have** - Reference existing imageId if nothing visual changed
5. **Screenshot loops** - If you've verified it once and nothing changed, move on
6. **Full DOM dump on complex pages** - Filter to interactive elements

## Before/After Comparisons

You cannot retroactively view old imageIds. Before taking a "before" screenshot:
- Note key visual details in text (spacing, colors, positions)
- Then make changes
- Then take "after" screenshot and compare against your notes

## Exit Criteria (When QA is Done)

Stop when:
- Change matches the specific requirement
- No console errors (`read_console_messages`)
- Interactive elements work (if applicable)

Do NOT keep screenshotting to make it "perfect" or to "make sure."

## Quick Reference

```
Structural check     → read_page filter:"interactive"
Text/attribute check → read_page filter:"interactive"  
Component styling    → zoom on region
Layout verification  → single screenshot
Final sign-off       → single screenshot
"Does it exist?"     → read_page, never screenshot
```
