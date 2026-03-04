# Building Skill Evaluations

Evaluations are your source of truth for measuring whether a skill actually improves Claude's behavior. Build them **before** writing extensive documentation.

## Contents

- [Why Evaluations First](#why-evaluations-first)
- [Evaluation Structure](#evaluation-structure)
- [Evaluation-Driven Development Process](#evaluation-driven-development-process)
- [Three Evaluation Types](#three-evaluation-types)
- [Claude A / Claude B Method](#claude-a--claude-b-method)
- [Evaluation Examples](#evaluation-examples)

---

## Why Evaluations First

Without evaluations, you're guessing at what the skill needs. With evaluations, you:

1. Know exactly what problems the skill is solving
2. Measure whether your skill actually fixes them
3. Avoid writing documentation for problems that don't exist
4. Have a regression test when you iterate

**Rule:** Write at least 3 evaluations before writing SKILL.md content.

---

## Evaluation Structure

```json
{
  "skills": ["your-skill-name"],
  "query": "The exact user request that should trigger the skill",
  "files": ["test-files/example.pdf"],
  "expected_behavior": [
    "Observable action Claude should take (verb + specific detail)",
    "Specific output or file that should be produced",
    "Specific rule or constraint that should be respected",
    "What Claude should NOT do (negative assertion)"
  ]
}
```

**Key fields:**
- `skills`: The skill name(s) that should be active
- `query`: A real user request — not synthetic test language
- `files`: Any input files needed for the test
- `expected_behavior`: Observable, specific outcomes — not vague goals

**Writing good expected_behavior:**
- ✅ "Reads the SKILL.md frontmatter and validates the `name` field format"
- ✅ "Outputs a before/after comparison for each identified issue"
- ✅ "Does NOT proceed without gathering at least 3 concrete examples"
- ❌ "Does a good job reviewing the skill"
- ❌ "Gives helpful feedback"

---

## Evaluation-Driven Development Process

### 1. Identify gaps (run Claude without the skill)

Run representative tasks with Claude without any skill active. Document specific failures:
- What context did you have to repeatedly provide?
- What mistakes did Claude make?
- What information did Claude lack?

### 2. Create evaluations covering the gaps

Build 3+ scenarios covering:
- The happy path (typical successful use)
- An edge case or ambiguous input
- A failure mode that the skill should handle gracefully

### 3. Establish baseline

Run each evaluation without the skill loaded. Note what Claude gets wrong.

### 4. Write minimal instructions

Write just enough SKILL.md content to make the evaluations pass. No more.

### 5. Iterate

If an evaluation fails, identify which instruction is missing or unclear. Update SKILL.md and re-run. Repeat until all evaluations pass.

---

## Three Evaluation Types

### Type 1: Happy Path
Tests the core use case works correctly.

```json
{
  "skills": ["crafting-skills"],
  "query": "Help me create a new skill for processing CSV files",
  "expected_behavior": [
    "Asks for at least 3 concrete examples of how the skill will be used",
    "Does not start writing SKILL.md until examples are gathered",
    "Proposes relevant bundled resources (scripts for CSV parsing, references for format docs)",
    "Produces a SKILL.md with a third-person description containing specific triggers"
  ]
}
```

### Type 2: Edge Case
Tests behavior with ambiguous or incomplete input.

```json
{
  "skills": ["crafting-skills"],
  "query": "Make a skill",
  "expected_behavior": [
    "Asks clarifying questions about the skill's purpose before proceeding",
    "Does not generate a generic SKILL.md without understanding the use case",
    "Identifies specific information needed: what tasks, what triggers, what outputs"
  ]
}
```

### Type 3: Review Mode
Tests the skill audit capability.

```json
{
  "skills": ["crafting-skills"],
  "query": "Review this skill for me",
  "files": ["test-files/poorly-written-skill/SKILL.md"],
  "expected_behavior": [
    "Reads the provided SKILL.md completely before evaluating",
    "Categorizes findings as Critical, Important, or Minor",
    "Provides a before/after fix for each Critical issue",
    "Identifies the first-person description as a Critical issue",
    "Offers to apply all fixes upon approval"
  ]
}
```

---

## Claude A / Claude B Method

The most effective way to develop and iterate on skills:

**Claude A** — the expert who helps build and refine the skill
**Claude B** — a fresh instance with the skill loaded, performing real tasks

### Creating skills

1. Complete a task with Claude A using normal prompting
2. Notice what context you repeatedly provide
3. Ask Claude A: "Create a skill that captures this pattern"
4. Ask Claude A to remove unnecessary explanations
5. Test with Claude B on similar tasks
6. Bring Claude B's struggles back to Claude A for refinement

### Iterating on skills

1. Use skill in real workflows with Claude B
2. Note where Claude B struggles or makes unexpected choices
3. Return to Claude A with specifics: "Claude B forgot to filter test accounts when asked for regional reports"
4. Claude A suggests fixes: more prominent rule, stronger language ("MUST filter"), restructured section
5. Apply and test again

### Observation signals

Watch for these in Claude B:
- **Unexpected file access order** → structure may not match intuition
- **Missed references** → links need to be more explicit
- **Overread files** → that content should move to SKILL.md
- **Never-accessed files** → file may be unnecessary or poorly signaled

---

## Evaluation Examples

### For crafting-skills specifically

These 3 evaluations test the key behaviors of this skill:

**Evaluation 1 — New skill creation with examples-first enforcement:**
```json
{
  "skills": ["crafting-skills"],
  "query": "I want to build a skill for my team's BigQuery data warehouse",
  "expected_behavior": [
    "Asks for concrete examples of user requests before writing any SKILL.md",
    "Identifies references/schema.md as a valuable bundled resource for table schemas",
    "Proposes domain-specific organization (finance.md, sales.md, etc.) for multiple domains",
    "Produces a description in third person with specific BigQuery triggers"
  ]
}
```

**Evaluation 2 — Skill review with critical issue detection:**
```json
{
  "skills": ["crafting-skills"],
  "query": "Can you review this skill? ---\nname: my helper\ndescription: I help you process your files\n---\n\n# My Helper\nSee details in docs/overview.md → docs/details.md",
  "expected_behavior": [
    "Flags the description as Critical (first person: 'I help you')",
    "Flags the name as Important (vague: 'my helper'; should be gerund form)",
    "Flags the nested reference chain (SKILL.md → overview.md → details.md) as Critical",
    "Provides concrete before/after fixes for all Critical issues"
  ]
}
```

**Evaluation 3 — Description quality gate:**
```json
{
  "skills": ["crafting-skills"],
  "query": "Write a description for my PDF skill",
  "expected_behavior": [
    "Asks what specific capabilities the skill provides before writing description",
    "Asks what user phrases should trigger the skill",
    "Produces a description that starts with action verbs (third person)",
    "Includes both WHAT the skill does and WHEN triggers in the description",
    "Description is under 1024 characters"
  ]
}
```
