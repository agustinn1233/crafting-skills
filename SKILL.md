---
name: crafting-skills
description: "Creates, reviews, and improves Claude Code skills following all Anthropic official best practices. Use when a developer wants to build a new skill from scratch with guidance on structure, naming, descriptions, freedom levels, and progressive disclosure; audit an existing SKILL.md or skill directory against the official quality checklist and find improvement opportunities; or generate evaluation scenarios to test a skill before publishing. Triggers on phrases like 'create a skill', 'build a skill', 'write a skill', 'review this skill', 'improve this skill', 'analyze this SKILL.md', 'evaluate my skill', 'help me make a skill', or 'skill best practices'."
---

# Crafting Skills

Guides creation, review, and evaluation of Claude Code skills following [Anthropic's official best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).

## Mode Selection

**Creating a new skill** → Follow [Create Workflow](#create-workflow)
**Reviewing an existing skill** → Follow [Review Workflow](#review-workflow)
**Generating test evaluations** → See [references/evaluation.md](references/evaluation.md)

---

## Create Workflow

Copy and track this checklist:

```
Create Progress:
- [ ] Step 1: Gather concrete examples
- [ ] Step 2: Plan reusable contents
- [ ] Step 3: Write bundled resources
- [ ] Step 4: Write SKILL.md
- [ ] Step 5: Self-review against checklist
- [ ] Step 6: Generate evaluations
```

### Step 1: Gather Concrete Examples

Before writing a single line, understand the use cases. Ask:

- "Give me 3 concrete user requests that should trigger this skill."
- "What would a user say that should NOT trigger this skill?"
- "What does a successful output look like?"

Do not proceed without at least 3 clear examples.

### Step 2: Plan Reusable Contents

For each example, identify what would help Claude execute the task repeatedly:

| Resource | Include when |
|----------|-------------|
| `scripts/` | Same code written repeatedly; deterministic/fragile operations |
| `references/` | Domain schemas, API docs, policies, large reference material |
| `assets/` | Templates, boilerplate, images used in output |

Only create resources that are actually needed. Delete example placeholder files.

### Step 3: Write Bundled Resources

Create `scripts/`, `references/`, and `assets/` files before writing SKILL.md.

**Scripts:**
- Handle errors explicitly — never punt to Claude
- Document every constant (no magic numbers)
- Use forward slashes in all paths
- Test by actually running the script

**References:**
- Add table of contents at top if >100 lines
- Organize by domain when multiple domains exist
- One clear topic per file

For patterns and templates → See [references/patterns.md](references/patterns.md)

### Step 4: Write SKILL.md

#### Frontmatter

```yaml
---
name: gerund-form-name        # max 64 chars, lowercase/numbers/hyphens
description: Third-person description of what it does. Use when [triggers]. # max 1024 chars
---
```

**Name rules:** max 64 chars · lowercase/numbers/hyphens only · no "anthropic" or "claude"
**Description rules:** third person always · max 1024 chars · includes WHAT + specific WHEN triggers

**Description formula:**
```
[Action verbs] [specific capabilities]. Use when [concrete triggers/scenarios].
```

Good example:
```yaml
description: Extracts text and tables from PDF files, fills forms, merges documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

Bad example:
```yaml
description: Helps with documents
```

#### Body

- Under 500 lines — split to reference files if larger
- Concise — Claude is already smart; only add context it doesn't have
- Match freedom level to task fragility (see [references/patterns.md](references/patterns.md))
- All file references one level deep from SKILL.md (never nested)
- Use checklists for multi-step workflows
- Consistent terminology throughout — pick one term, use it always

### Step 5: Self-Review Against Checklist

Run every item → See [references/checklist.md](references/checklist.md)

Fix all Critical and Important issues before proceeding.

### Step 6: Generate Evaluations

Build ≥3 evaluation scenarios before sharing → See [references/evaluation.md](references/evaluation.md)

---

## Review Workflow

When given an existing skill to audit:

1. Read the full SKILL.md (frontmatter + body)
2. Note all bundled resource files referenced
3. Run through every item in [references/checklist.md](references/checklist.md)
4. Categorize findings:

**Critical** — breaks skill discovery or produces incorrect output:
- Description written in first or second person
- Description vague/missing triggers
- Nested references (more than 1 level deep from SKILL.md)
- Windows-style paths (`\`) anywhere in the skill

**Important** — reduces quality significantly:
- SKILL.md body >500 lines without splitting
- No feedback loops for quality-critical tasks
- Time-sensitive information in main content
- Inconsistent terminology

**Minor** — polish and best practice alignment:
- Non-gerund name when gerund would be clearer
- Long reference files (>100 lines) without a table of contents
- Too many options presented without a clear default

5. For each finding, provide:
   - **Issue**: What's wrong and why it matters
   - **Fix**: Before/after showing the concrete improvement

6. Offer to apply all fixes if developer approves.

---

## Core Principles (Quick Reference)

- **Context window is shared** — every token must justify its cost
- **Descriptions are the discovery mechanism** — write them last, after skill is complete
- **Test with Haiku, Sonnet, and Opus** — what works for Opus may need more detail for Haiku
- **Evaluation-first** — build evaluations before extensive documentation
- **MCP tools** — always reference as `ServerName:tool_name` to avoid "tool not found"
- **Default + escape hatch** — provide one recommended approach, then note exceptions

For anti-patterns to avoid → See [references/anti-patterns.md](references/anti-patterns.md)
