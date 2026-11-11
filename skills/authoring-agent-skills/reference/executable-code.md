# Skills with scripts, dependencies, and MCP tools

Condensed from [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices#advanced-skills-with-executable-code).

## Prefer bundled scripts for deterministic operations

A pre-made script is more reliable than generated code, is consistent across uses, and costs only its output in tokens because Claude runs it without loading its source.

State the intent for each script:

- **Run it** (the usual case): "Run `scripts/analyze_form.py` to extract fields"
- **Read it as reference**: "See `scripts/analyze_form.py` for the field extraction algorithm"

Document each script with its command and output format:

````markdown
**analyze_form.py**: Extract all form fields from a PDF

```bash
python scripts/analyze_form.py input.pdf > fields.json
```

Output format:
```json
{"field_name": {"type": "text", "x": 100, "y": 200}}
```
````

## Solve, don't defer

Scripts handle error conditions themselves instead of failing and leaving Claude to work it out.

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
```

Justify every constant. If the author does not know why a value was chosen, Claude cannot tell either.

```python
# HTTP requests typically complete within 30 seconds
REQUEST_TIMEOUT = 30
```

## Plan, validate, execute

For batch operations, destructive changes, or complex rules, have Claude write a structured plan file (for example `changes.json`), validate it with a script, and only then apply it: analyze → create plan file → validate plan → execute → verify.

Make validators verbose so Claude can fix problems from the message alone:

```
Field 'signature_date' not found. Available fields: customer_name, order_total, signature_date_signed
```

## Visual analysis

When an input can be rendered as an image (a PDF page, a layout), convert it and have Claude inspect the image to identify structure.

## Dependencies

- List required packages in `SKILL.md` and give the install command; do not assume anything is installed.
- Runtime differs by surface: claude.ai can install packages from npm and PyPI and pull from GitHub; the Claude API code execution environment has no network access and no runtime installation. Verify availability against the [code execution tool documentation](https://platform.claude.com/docs/en/agents-and-tools/tool-use/code-execution-tool).

```markdown
Install the required package: `pip install pypdf`
```

## MCP tool references

Use fully qualified names, `ServerName:tool_name`, so the tool resolves when several MCP servers are connected:

```markdown
Use the BigQuery:bigquery_schema tool to retrieve table schemas.
```
