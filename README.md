# Smart Screenshot QA Skill

A Claude Code skill that dramatically reduces token consumption during browser-based QA by choosing the most efficient verification method for each check.

## The Problem

When using Claude Code for frontend QA, it's easy to fall into expensive patterns:
- Taking full-page screenshots after every small change
- Using screenshots to verify things the DOM can tell you instantly
- Getting stuck in screenshot loops trying to make things "perfect"
- Dumping the entire DOM when you only need interactive elements

These habits can burn through 10-50x more tokens than necessary.

## The Solution

This skill teaches Claude to pick the cheapest verification method that actually answers the question:

| Verification Need | Best Method | Token Cost |
|-------------------|-------------|------------|
| Does element exist? | DOM inspection | ~50 |
| Check text/attributes | DOM inspection | ~50 |
| Component styling | Targeted zoom | 100-200 |
| Layout/alignment | Single screenshot | 700-1500 |
| Full DOM dump | Avoid | Up to 12,500 |

## Installation

Copy `SKILL.md` to your Claude Code skills directory, or reference it in your project's `.claude/skills/` folder.

## Usage

The skill activates automatically when Claude Code detects browser-based QA tasks. You can also invoke it explicitly:

```
/smart-screenshot-qa
```

## Key Principles

1. **DOM first** - If you can answer the question with `read_page`, don't take a screenshot
2. **Zoom over full screenshots** - Use targeted region captures for component checks
3. **Batch changes** - Make 3-5 changes before verifying visually
4. **Exit when done** - One verification that passes = move on

## Example Savings

| Task | Without Skill | With Skill | Savings |
|------|---------------|------------|---------|
| Verify button exists | 1,200 tokens | 50 tokens | 96% |
| Check 5 form fields | 6,000 tokens | 250 tokens | 96% |
| Style a component | 4,800 tokens | 400 tokens | 92% |

## License

MIT
