# Smart Screenshot QA Skill

A Claude Code skill that reduces token consumption during browser-based QA by choosing the most efficient verification method for each check.

## The Problem

When using Claude Code for frontend QA, it's easy to fall into expensive patterns:

- Taking full-page screenshots after every small change
- Using screenshots to verify things the DOM can tell you instantly
- Getting stuck in screenshot loops trying to make things "perfect"
- Dumping the entire DOM when you only need interactive elements

These habits can burn through 10-50x more tokens than necessary.

## The Solution

This skill teaches Claude to pick the right verification method for the job:

| Verification Need | Best Method | Token Cost |
|-------------------|-------------|------------|
| Does element exist? | DOM inspection (filtered) | 800-2,000 |
| Check text/attributes | DOM inspection (filtered) | 800-2,000 |
| Component styling | Targeted zoom | 100-200 |
| Layout/alignment | Single screenshot | 700-1,500 |
| Full DOM dump | Avoid | 6,000-25,000+ |

**Key insight:** Filtered DOM and full screenshots are comparable in cost. The real wins are targeted zoom (5-10x cheaper than full screenshots) and avoiding full DOM dumps. DOM is *faster* for structural questions, not necessarily cheaper.

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

## Example Savings

| Task | Without Skill | With Skill | Savings |
|------|---------------|------------|---------|
| Verify button exists | 1,500 tokens (screenshot) | 1,000 tokens (DOM) | 33% + faster |
| Check 5 components | 7,500 tokens (5 screenshots) | 750 tokens (5 zooms) | 90% |
| Style a component | 4,500 tokens (3 screenshots) | 400 tokens (2 zooms) | 91% |

## License

MIT
