# Skill Anti-Patterns

Common mistakes that break skill discovery, reduce quality, or waste context tokens. Each entry shows what's wrong and how to fix it.

## Contents

- [Windows-Style Paths](#windows-style-paths)
- [Too Many Options](#too-many-options)
- [Vague Descriptions](#vague-descriptions)
- [Wrong Person in Description](#wrong-person-in-description)
- [Nested References](#nested-references)
- [Time-Sensitive Information](#time-sensitive-information)
- [Inconsistent Terminology](#inconsistent-terminology)
- [Over-Explaining](#over-explaining)
- [Magic Numbers](#magic-numbers)
- [Punting Errors to Claude](#punting-errors-to-claude)
- [Assuming Dependencies Are Installed](#assuming-dependencies-are-installed)

---

## Windows-Style Paths

**Bad:**
```markdown
Run: `python scripts\analyze_form.py`
See: `references\schema.md`
```

**Good:**
```markdown
Run: `python scripts/analyze_form.py`
See: `references/schema.md`
```

Unix-style paths work on all platforms. Windows paths break on macOS and Linux.

---

## Too Many Options

**Bad:**
```markdown
You can use pypdf, or pdfplumber, or PyMuPDF, or pdf2image, or pdfminer...
```

**Good:**
```markdown
Use pdfplumber for text extraction:
```python
import pdfplumber
```

For scanned PDFs requiring OCR, use pdf2image with pytesseract instead.
```

Provide one default with a clear escape hatch for exceptions. Don't list all options.

---

## Vague Descriptions

**Bad:**
```yaml
description: Helps with documents
description: Processes data
description: Does stuff with files
description: A useful helper skill
```

**Good:**
```yaml
description: Extracts text and tables from PDF files, fills forms, merges documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.
```

Vague descriptions cause missed triggers. Include specific capabilities AND specific trigger phrases.

---

## Wrong Person in Description

**Bad:**
```yaml
description: I can help you process Excel files and generate reports.
description: You can use this skill to analyze spreadsheet data.
description: This skill helps me understand your document needs.
```

**Good:**
```yaml
description: Analyzes Excel spreadsheets, creates pivot tables, generates reports. Use when working with .xlsx files or when the user asks about spreadsheet analysis.
```

The description is injected into the system prompt. First/second person causes discovery failures.

---

## Nested References

**Bad:**
```
SKILL.md → references/advanced.md → references/details.md → references/api.md
```

```markdown
# SKILL.md
See [advanced.md](references/advanced.md) for details.

# advanced.md
See [details.md](references/details.md) for more.

# details.md
Here's the actual information...
```

**Good:**
```
SKILL.md → references/advanced.md
SKILL.md → references/details.md
SKILL.md → references/api.md
```

```markdown
# SKILL.md
**Advanced features**: See [references/advanced.md](references/advanced.md)
**API details**: See [references/api.md](references/api.md)
```

Claude may use `head -100` to preview nested files and miss critical information. Keep all links one level deep from SKILL.md.

---

## Time-Sensitive Information

**Bad:**
```markdown
If you're doing this before August 2025, use the old API endpoint.
After August 2025, use the new API endpoint.
```

**Good:**
```markdown
## Current method

Use the v2 API endpoint: `api.example.com/v2/messages`

## Old patterns

<details>
<summary>Legacy v1 API (deprecated 2025-08)</summary>

The v1 API used: `api.example.com/v1/messages`
This endpoint is no longer supported.
</details>
```

Time-based conditionals become wrong over time. Use "current method" + "old patterns" sections.

---

## Inconsistent Terminology

**Bad:**
```markdown
Submit the form using the API endpoint.
...
Call the URL with your data.
...
Hit the route to create a record.
...
POST to the path when ready.
```

**Good:**
```markdown
Send a POST request to the API endpoint.
...
Include your data in the request body.
...
The API endpoint returns a JSON response.
```

Pick one term per concept and use it everywhere:
- ✅ Always "API endpoint" — never mix with "URL", "route", "path"
- ✅ Always "field" — never mix with "box", "element", "control"
- ✅ Always "extract" — never mix with "pull", "get", "retrieve"

---

## Over-Explaining

**Bad (~150 tokens):**
```markdown
## Extract PDF text

PDF (Portable Document Format) files are a common file format that contains
text, images, and other content. To extract text from a PDF, you'll need to
use a library. There are many libraries available for PDF processing, but
pdfplumber is recommended because it's easy to use and handles most cases well.
First, you'll need to install it using pip. Then you can use the code below...
```

**Good (~50 tokens):**
````markdown
## Extract PDF text

Install: `pip install pdfplumber`

```python
import pdfplumber
with pdfplumber.open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```
````

Claude already knows what PDFs are. Challenge every sentence: "Does Claude really need this?"

---

## Magic Numbers

**Bad:**
```python
TIMEOUT = 47
RETRIES = 5
BATCH_SIZE = 250
```

**Good:**
```python
# HTTP requests typically complete within 30 seconds
# Extra buffer accounts for slow connections
REQUEST_TIMEOUT = 30

# Three retries balances reliability vs speed
# Most intermittent failures resolve by second retry
MAX_RETRIES = 3

# Process 100 records per batch to stay within API rate limits
# See: https://api.example.com/docs/rate-limits
BATCH_SIZE = 100
```

Undocumented constants are "voodoo" — Claude (and humans) can't know if changing them is safe.

---

## Punting Errors to Claude

**Bad:**
```python
def process_file(path):
    # Let Claude figure it out if this fails
    return open(path).read()
```

**Good:**
```python
def process_file(path):
    """Process file, returning empty string if not found."""
    try:
        with open(path) as f:
            return f.read()
    except FileNotFoundError:
        print(f"Warning: {path} not found, using empty content")
        return ""
    except PermissionError:
        print(f"Error: Cannot read {path} — check file permissions")
        return ""
```

Scripts should handle failure cases, not punt them back to Claude to figure out.

---

## Assuming Dependencies Are Installed

**Bad:**
```markdown
Use pdfplumber to extract text from the PDF file.
```

**Good:**
```markdown
Install required package: `pip install pdfplumber`

Then extract text:
```python
from pdfplumber import open as pdf_open

with pdf_open("file.pdf") as pdf:
    text = pdf.pages[0].extract_text()
```
```

Never assume packages are available. Always include the install command.
