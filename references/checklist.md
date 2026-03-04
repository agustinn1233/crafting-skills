# Official Skill Quality Checklist

Source: [Anthropic Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

Run through all items before publishing. Mark each ✅ (pass) or ❌ (fail with note).

## Contents

- [Core Quality](#core-quality)
- [Code and Scripts](#code-and-scripts)
- [Testing](#testing)

---

## Core Quality

- [ ] **Description is specific** — includes key terms that appear in real user requests
- [ ] **Description includes triggers** — both WHAT the skill does AND WHEN to use it
- [ ] **Description is third person** — "Processes Excel files" not "I can help you" or "You can use this to"
- [ ] **SKILL.md body is under 500 lines** — split to reference files if approaching limit
- [ ] **Additional details in separate files** — reference files exist and are linked from SKILL.md
- [ ] **No time-sensitive information** — no dates like "after August 2025, use the new API"; use "old patterns" section for deprecated content
- [ ] **Consistent terminology** — one term per concept used throughout (never mix "API endpoint" / "URL" / "route")
- [ ] **Examples are concrete** — input/output pairs for quality-dependent output, not abstract descriptions
- [ ] **References are one level deep** — all files linked directly from SKILL.md; no SKILL.md → A → B chains
- [ ] **Progressive disclosure used** — SKILL.md points to reference files for details; heavy content offloaded
- [ ] **Workflows have clear steps** — multi-step processes use numbered steps or checklists
- [ ] **Name follows conventions** — gerund form preferred (processing-pdfs); no vague names (helper, utils); no reserved words
- [ ] **Name validation** — max 64 chars · lowercase/numbers/hyphens only · no "anthropic" or "claude"
- [ ] **Long reference files have ToC** — any reference file >100 lines has a table of contents at top
- [ ] **Forward slashes in all paths** — no Windows-style backslashes anywhere
- [ ] **Freedom level appropriate** — low freedom (exact commands) for fragile ops; high freedom (text guidance) for flexible tasks
- [ ] **Default provided** — when multiple approaches exist, one is recommended with note for exceptions

---

## Code and Scripts

- [ ] **Scripts solve, don't punt** — error conditions handled explicitly; no bare `open()` without try/except
- [ ] **No magic numbers** — every constant is documented with why that value
- [ ] **Required packages listed** — all dependencies declared in SKILL.md instructions
- [ ] **Scripts are documented** — clear docstrings and usage examples for each script
- [ ] **No Windows-style paths** — all paths use forward slashes
- [ ] **Validation steps included** — plan-validate-execute pattern for risky/batch operations
- [ ] **Feedback loops present** — for quality-critical tasks: run → check → fix → repeat cycle
- [ ] **Execution vs read intent clear** — instructions say "Run script X" or "See script X for algorithm"
- [ ] **No assumed installations** — don't write "use pdfplumber"; write "Install: `pip install pdfplumber`, then use..."
- [ ] **MCP tools fully qualified** — always `ServerName:tool_name` format, never just `tool_name`

---

## Testing

- [ ] **At least 3 evaluations created** — covering distinct use cases
- [ ] **Baseline measured** — tested without the skill to understand what it actually adds
- [ ] **Tested with Haiku** — skill provides enough guidance for the smaller model
- [ ] **Tested with Sonnet** — skill is clear and efficient
- [ ] **Tested with Opus** — skill doesn't over-explain what the model already knows
- [ ] **Real usage scenarios tested** — not just synthetic test cases
- [ ] **Edge cases covered** — evaluations include ambiguous or tricky inputs

---

## Evaluation Template

```json
{
  "skills": ["your-skill-name"],
  "query": "User request that should trigger the skill",
  "files": ["test-files/example.pdf"],
  "expected_behavior": [
    "Specific observable action Claude should take",
    "Specific output or file that should be produced",
    "Specific rule or constraint that should be respected"
  ]
}
```

For detailed evaluation guidance → See [evaluation.md](evaluation.md)
