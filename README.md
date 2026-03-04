# crafting-skills

A Claude Code skill for creating, reviewing, and improving agent skills following [Anthropic's official best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).

## Install

```bash
npx skills add aguferreira/crafting-skills
```

## What it does

- **Create** — Guides you through building a new skill from scratch: gathering examples, planning resources, writing concise SKILL.md files, and choosing the right freedom level
- **Review** — Audits any existing skill against the official Anthropic checklist and categorizes findings as Critical / Important / Minor with before/after fixes
- **Evaluate** — Generates evaluation scenarios (≥3) to test a skill before publishing

## Usage

Just describe what you need in natural language:

```
"Help me create a skill for processing CSV files"
"Review this SKILL.md for issues"
"Audit my skill against best practices"
"Generate evaluation scenarios for my skill"
```

## What's included

```
crafting-skills/
├── SKILL.md                    # Core workflow — loaded on trigger (~130 lines)
└── references/
    ├── checklist.md            # Full official Anthropic quality checklist (27 items)
    ├── patterns.md             # Freedom levels, naming, templates, I/O examples
    ├── anti-patterns.md        # 11 anti-patterns with before/after fixes
    └── evaluation.md           # Evaluation-driven development guide + 3 real examples
```

Uses progressive disclosure — reference files are only loaded when needed, keeping context usage minimal.

## Skill best practices applied

This skill dog-foods its own guidelines:

- Description in third person with specific trigger phrases
- SKILL.md under 500 lines with all details in `references/`
- All file references one level deep from SKILL.md
- No time-sensitive information
- Consistent terminology throughout
- Copyable workflow checklist for multi-step processes
