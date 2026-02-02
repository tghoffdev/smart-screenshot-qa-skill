# Smart Screenshot QA Skill

A Claude Code skill that stops screenshot spirals and dramatically reduces token consumption during browser-based QA.

## The Problem

When using Claude Code for frontend QA, the real token killer isn't individual screenshots—it's **loops**:

```
change → screenshot → tweak → screenshot → "let me check" → screenshot → "one more" → screenshot
```

5 unnecessary screenshots = 3,500-7,500 wasted tokens. Per component. Per session.

Other costly patterns:
- Full-page screenshots when you only need one component
- Screenshots to verify things the DOM can answer instantly
- Full DOM dumps on complex pages (can cost 15-20x more than a screenshot)

## Token Costs (Tested on GitHub.com)

| Method | Tokens | Best For |
|--------|--------|----------|
| Targeted zoom (region) | 100-200 | Component styling, specific elements |
| `find` (natural language) | 500-1,000 | "Is there a submit button?" without full DOM |
| `read_page` (filter: "interactive") | 800-2,000 | Structural checks, element existence |
| Full viewport screenshot | 1,000-1,500 | Layout verification, final sign-off |
| `read_page` (full DOM) | 6,000-25,000+ | Avoid on complex pages |

**Key insights:**
- Targeted zoom is **5-10x cheaper** than full screenshots
- `find` is great for natural language queries without dumping the whole DOM
- Filtered DOM is often cheaper AND faster for structural questions
- Full DOM can cost **15-20x more** than a screenshot

## The Solution

This skill teaches Claude to:

1. **Use targeted zoom** for component checks (5-10x cheaper than full screenshots)
2. **Use `find`** for natural language element queries (mid-cost, avoids full DOM)
3. **Use filtered DOM** for structural questions (`filter: "interactive"` saves 60-80%)
4. **Batch changes** before taking screenshots (3-5 changes, then verify)
5. **Exit when done** - One verification that passes = move on

## Installation

Copy `SKILL.md` to your Claude Code skills directory, or reference it in your project's `.claude/skills/` folder.

## Usage

The skill activates automatically when Claude Code detects browser-based QA tasks. You can also invoke it explicitly:

```
/smart-screenshot-qa
```

## Decision Tree

| Question | Method |
|----------|--------|
| "Is there a [thing]?" | `find` (natural language) |
| Does element exist? | `read_page` filter:"interactive" |
| Check text/attributes? | `read_page` filter:"interactive" |
| Component look right? | Targeted zoom |
| Layout/alignment correct? | Single screenshot (after batching) |
| Final sign-off? | One screenshot, then stop |

## Anti-Patterns to Avoid

1. **Screenshot loops** - One verification that passes = done
2. **Full screenshot for one component** - Use targeted zoom (5-10x cheaper)
3. **Screenshot after every change** - Batch 3-5 changes first
4. **Full DOM dump** - Always use `filter: "interactive"` or `find`

## Note on imageIds

Screenshot imageIds persist for the browser session but disappear from context when it gets summarized. If you need to reference a screenshot later in a long session, note the key visual details in text immediately after taking it.

## Example Savings

| Scenario | Without Skill | With Skill | Savings |
|----------|---------------|------------|---------|
| Verify button styling | 1,200 tokens (full screenshot) | 150 tokens (zoom) | 87% |
| "Is there a submit button?" | 2,000 tokens (filtered DOM) | 700 tokens (find) | 65% |
| Check 5 elements exist | 6,000 tokens (5 screenshots) | 1,500 tokens (1 DOM read) | 75% |
| QA session with loops | 15,000 tokens | 2,500 tokens | 83% |

## License

MIT
