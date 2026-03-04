# Skill Patterns Reference

## Contents

- [Freedom Levels](#freedom-levels)
- [Naming Conventions](#naming-conventions)
- [Description Formula](#description-formula)
- [Progressive Disclosure Patterns](#progressive-disclosure-patterns)
- [Workflow Checklist Pattern](#workflow-checklist-pattern)
- [Feedback Loop Pattern](#feedback-loop-pattern)
- [Template Pattern](#template-pattern)
- [Examples Pattern](#examples-pattern)
- [Conditional Workflow Pattern](#conditional-workflow-pattern)
- [Script Patterns](#script-patterns)

---

## Freedom Levels

Match specificity to task fragility.

### High Freedom — text guidance
Use when: multiple approaches are valid; decisions depend on context.

```markdown
## Code review process

1. Analyze code structure and organization
2. Check for potential bugs or edge cases
3. Suggest improvements for readability and maintainability
4. Verify adherence to project conventions
```

### Medium Freedom — pseudocode/parameters
Use when: a preferred pattern exists but some variation is acceptable.

````markdown
## Generate report

Use this template, customize as needed:

```python
def generate_report(data, format="markdown", include_charts=True):
    # Process data
    # Generate output in specified format
    # Optionally include visualizations
```
````

### Low Freedom — exact scripts
Use when: operations are fragile, consistency is critical, exact sequence required.

````markdown
## Database migration

Run exactly this script:

```bash
python scripts/migrate.py --verify --backup
```

Do not modify the command or add additional flags.
````

**Analogy:** Narrow bridge with cliffs = low freedom (guardrails required). Open field = high freedom (many paths work).

---

## Naming Conventions

**Preferred — gerund form:**
- `processing-pdfs`
- `analyzing-spreadsheets`
- `managing-databases`
- `testing-code`
- `writing-documentation`

**Acceptable:**
- Noun phrases: `pdf-processing`, `spreadsheet-analysis`
- Action-oriented: `process-pdfs`, `analyze-spreadsheets`

**Avoid:**
- Vague: `helper`, `utils`, `tools`, `documents`, `data`, `files`
- Reserved words: `anthropic-helper`, `claude-tools`

---

## Description Formula

```
[Action verbs] [specific capabilities]. Use when [concrete triggers/scenarios].
```

**Good examples:**

```yaml
# PDF Processing
description: Extracts text and tables from PDF files, fills forms, merges documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.

# Excel Analysis
description: Analyzes Excel spreadsheets, creates pivot tables, generates charts. Use when analyzing Excel files, spreadsheets, tabular data, or .xlsx files.

# Git Commit Helper
description: Generates descriptive commit messages by analyzing git diffs. Use when the user asks for help writing commit messages or reviewing staged changes.
```

**Bad examples:**

```yaml
description: Helps with documents          # Vague, no triggers
description: Processes data               # Vague, no triggers
description: I can help you with PDFs     # First person — NEVER
description: You can use this for PDFs    # Second person — NEVER
```

---

## Progressive Disclosure Patterns

### Pattern 1: High-level guide with references

````markdown
# PDF Processing

## Quick start

```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```

## Advanced features

**Form filling**: See [FORMS.md](FORMS.md) for complete guide
**API reference**: See [REFERENCE.md](REFERENCE.md) for all methods
**Examples**: See [EXAMPLES.md](EXAMPLES.md) for common patterns
````

### Pattern 2: Domain-specific organization

```
bigquery-skill/
├── SKILL.md (overview and navigation)
└── reference/
    ├── finance.md (revenue, billing metrics)
    ├── sales.md (opportunities, pipeline)
    ├── product.md (API usage, features)
    └── marketing.md (campaigns, attribution)
```

SKILL.md:
```markdown
## Available datasets

**Finance**: Revenue, ARR, billing → See [reference/finance.md](reference/finance.md)
**Sales**: Opportunities, pipeline → See [reference/sales.md](reference/sales.md)
**Product**: API usage, features → See [reference/product.md](reference/product.md)
```

### Pattern 3: Conditional details

```markdown
## DOCX Processing

**Creating documents**: Use docx-js for new documents. See [DOCX-JS.md](DOCX-JS.md).
**Editing existing**: Modify the XML directly.

**For tracked changes**: See [REDLINING.md](REDLINING.md)
**For OOXML details**: See [OOXML.md](OOXML.md)
```

**Critical rule:** Never chain references. All links must be one level deep from SKILL.md.

---

## Workflow Checklist Pattern

For complex multi-step processes, always provide a copyable checklist:

````markdown
## PDF form filling workflow

Copy this checklist and check off items as you complete them:

```
Task Progress:
- [ ] Step 1: Analyze the form (run analyze_form.py)
- [ ] Step 2: Create field mapping (edit fields.json)
- [ ] Step 3: Validate mapping (run validate_fields.py)
- [ ] Step 4: Fill the form (run fill_form.py)
- [ ] Step 5: Verify output (run verify_output.py)
```

**Step 1: Analyze the form**
Run: `python scripts/analyze_form.py input.pdf`
...
````

---

## Feedback Loop Pattern

The validator → fix → repeat cycle for quality-critical tasks:

```markdown
## Document editing process

1. Make your edits to `word/document.xml`
2. **Validate immediately**: `python ooxml/scripts/validate.py unpacked_dir/`
3. If validation fails:
   - Review the error message carefully
   - Fix the issues in the XML
   - Run validation again
4. **Only proceed when validation passes**
5. Rebuild: `python ooxml/scripts/pack.py unpacked_dir/ output.docx`
```

The key is "only proceed when X passes" — this prevents skipping the loop.

---

## Template Pattern

### Strict template (use for APIs, data formats)

````markdown
## Report structure

ALWAYS use this exact template:

```markdown
# [Analysis Title]

## Executive summary
[One-paragraph overview]

## Key findings
- Finding 1 with supporting data

## Recommendations
1. Specific actionable recommendation
```
````

### Flexible template (use when adaptation is useful)

````markdown
## Report structure

Sensible default — use your best judgment based on the analysis:

```markdown
# [Analysis Title]

## Executive summary
[Overview]

## Key findings
[Adapt sections based on what you discover]
```

Adjust sections as needed for the specific analysis type.
````

---

## Examples Pattern

For output quality that depends on seeing patterns, provide input/output pairs:

````markdown
## Commit message format

**Example 1:**
Input: Added user authentication with JWT tokens
Output:
```
feat(auth): implement JWT-based authentication

Add login endpoint and token validation middleware
```

**Example 2:**
Input: Fixed bug where dates displayed incorrectly in reports
Output:
```
fix(reports): correct date formatting in timezone conversion

Use UTC timestamps consistently across report generation
```

Follow this style: type(scope): brief description, then detailed explanation.
````

---

## Conditional Workflow Pattern

```markdown
## Document modification workflow

1. Determine the modification type:

   **Creating new content?** → Follow "Creation workflow" below
   **Editing existing content?** → Follow "Editing workflow" below

2. Creation workflow:
   - Use docx-js library
   - Build document from scratch

3. Editing workflow:
   - Unpack existing document
   - Modify XML directly
   - Validate after each change
```

---

## Script Patterns

### Handle errors — never punt to Claude

**Good:**
```python
def process_file(path):
    """Process a file, creating it if it doesn't exist."""
    try:
        with open(path) as f:
            return f.read()
    except FileNotFoundError:
        print(f"File {path} not found, creating default")
        with open(path, "w") as f:
            f.write("")
        return ""
    except PermissionError:
        print(f"Cannot access {path}, using /tmp/fallback")
        return ""
```

**Bad:**
```python
def process_file(path):
    return open(path).read()  # Punts to Claude on error
```

### Document constants — no voodoo numbers

**Good:**
```python
# HTTP requests typically complete within 30 seconds
# Longer timeout accounts for slow connections
REQUEST_TIMEOUT = 30

# Three retries balances reliability vs speed
# Most intermittent failures resolve by the second retry
MAX_RETRIES = 3
```

**Bad:**
```python
TIMEOUT = 47  # Why 47?
RETRIES = 5   # Why 5?
```

### Verbose validation errors

Make validation scripts specific so Claude can self-correct:

```python
# Good error message
raise ValueError(
    f"Field 'signature_date' not found. "
    f"Available fields: {', '.join(available_fields)}"
)

# Bad error message
raise ValueError("Invalid field")
```
