# Smart Screenshot QA Skill

A Claude Code skill that reduces token consumption during browser-based QA by choosing the most efficient verification method for each check.

> **Note:** This skill is designed for Claude Code with the Chrome extension (Claude in Chrome MCP). The tool names (`zoom`, `find`, `read_page`) are specific to that environment.

## The Problem

When using Claude Code for frontend QA, it's easy to fall into expensive patterns:

```
change → screenshot → tweak → screenshot → "let me check" → screenshot → "one more" → screenshot
```

**5 unnecessary screenshots = 3,500-7,500 wasted tokens. Per component. Per session.**

Other costly patterns:
- Full-page screenshots when you only need one component
- Screenshots to verify things the DOM can tell you faster
- Dumping the entire DOM when you only need interactive elements

These habits can burn through 10-50x more tokens than necessary.

## The Solution

This skill teaches Claude to pick the right verification method for the job:

| Verification Need | Best Method | Token Cost |
|-------------------|-------------|------------|
| "Is there a submit button?" | `find` (natural language) | 500-1,000 |
| Does element exist? | DOM inspection (filtered) | 800-2,000 |
| Check text/attributes | DOM inspection (filtered) | 800-2,000 |
| Component styling | Targeted zoom | 100-200 |
| Layout/alignment | Single screenshot | 700-1,500 |
| Full DOM dump | **Avoid** | 6,000-25,000+ |

**Key insight:** Filtered DOM and full screenshots are comparable in cost. The real wins are targeted zoom (5-10x cheaper than full screenshots) and avoiding full DOM dumps. DOM is *faster* for structural questions, not necessarily cheaper.

## When NOT to Use This Skill

This skill optimizes for verify-and-move-on. Skip it when:
- Visual regression testing requires systematic before/after comparisons
- Accessibility audits need comprehensive coverage
- QA specs explicitly require thoroughness over efficiency

## Installation

Copy `SKILL.md` to your Claude Code skills directory, or reference it in your project's `.claude/skills/` folder.

## Usage

The skill activates automatically when Claude Code detects browser-based QA tasks. You can also invoke it explicitly:

```
/smart-screenshot-qa
```

## Key Principles

1. **DOM for structure** - If you need to check element existence or attributes, DOM is faster
2. **Zoom over full screenshots** - Use targeted region captures for component styling (5-10x cheaper)
3. **Batch changes** - Make 3-5 styling changes before verifying. For complex layout (flexbox, z-index), verify sooner
4. **Exit when done** - One verification that passes = move on
5. **Avoid full DOM dumps** - Always filter to interactive elements
6. **Don't retry failed zooms** - Fall back to full screenshot immediately

## Example Savings

| Task | Without Skill | With Skill | Savings |
|------|---------------|------------|---------|
| Verify button exists | 1,500 tokens (screenshot) | 1,000 tokens (DOM) | 33% + faster |
| Check 5 components | 7,500 tokens (5 screenshots) | 750 tokens (5 zooms) | 90% |
| Style a component | 4,500 tokens (3 screenshots) | 400 tokens (2 zooms) | 91% |

## License

MIT
