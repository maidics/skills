# Structure and content patterns

Condensed from [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices).

## Contents

- Progressive disclosure layouts
- Workflow with checklist
- Feedback loop
- Conditional workflow
- Output template
- Input/output examples
- Old patterns section

## Progressive disclosure layouts

Only `name` and `description` are loaded at startup. `SKILL.md` is read when the Skill triggers, and other files only when Claude opens them, so bundled reference material costs no context until used. Choose one layout:

**High-level guide with references**: quick start in `SKILL.md`, detail in linked files.

```markdown
## Advanced features

**Form filling**: See [FORMS.md](FORMS.md) for the complete guide
**API reference**: See [REFERENCE.md](REFERENCE.md) for all methods
```

**Domain-specific organization**: one file per domain so unrelated domains are never loaded.

```
bigquery-skill/
├── SKILL.md              (overview and navigation)
└── reference/
    ├── finance.md        (revenue, billing metrics)
    ├── sales.md          (opportunities, pipeline)
    └── product.md        (API usage, features)
```

**Conditional details**: common path inline, rare paths linked.

```markdown
## Editing documents

For simple edits, modify the XML directly.

**For tracked changes**: See [REDLINING.md](REDLINING.md)
```

Link every file directly from `SKILL.md`. Claude may only preview a file reached through a chain of references (`SKILL.md` → `advanced.md` → `details.md`) and miss content.

## Workflow with checklist

Break complex operations into sequential steps. For long ones, provide a checklist Claude copies into its response and checks off.

````markdown
## PDF form filling workflow

Copy this checklist and check off items as you complete them:

```
- [ ] Step 1: Analyze the form (run analyze_form.py)
- [ ] Step 2: Create field mapping (edit fields.json)
- [ ] Step 3: Validate mapping (run validate_fields.py)
- [ ] Step 4: Fill the form (run fill_form.py)
- [ ] Step 5: Verify output (run verify_output.py)
```

**Step 3: Validate mapping**

Run: `python scripts/validate_fields.py fields.json`

Fix any validation errors before continuing.
````

The same pattern works for Skills without code (research synthesis, content review). If a workflow grows large, move it to its own file and tell Claude which file to read for which task.

## Feedback loop

Run validator → fix errors → repeat. The validator can be a script or a reference document Claude compares against.

```markdown
1. Make your edits to `word/document.xml`
2. **Validate immediately**: `python ooxml/scripts/validate.py unpacked_dir/`
3. If validation fails, fix the issues and run validation again
4. **Only proceed when validation passes**
5. Rebuild: `python ooxml/scripts/pack.py unpacked_dir/ output.docx`
```

## Conditional workflow

Guide Claude through decision points explicitly.

```markdown
1. Determine the modification type:

   **Creating new content?** → Follow "Creation workflow" below
   **Editing existing content?** → Follow "Editing workflow" below
```

## Output template

Match strictness to the need. For strict formats (API responses, data files), say "ALWAYS use this exact template structure" and give the template. For flexible output, offer it as a default:

````markdown
## Report structure

Here is a sensible default format, but use your best judgment based on the analysis:

```markdown
# [Analysis Title]

## Executive summary
[Overview]

## Key findings
[Adapt sections based on what you discover]
```
````

## Input/output examples

When output quality depends on style, give two or three input/output pairs rather than a description.

````markdown
**Example 1:**
Input: Added user authentication with JWT tokens
Output:
```
feat(auth): implement JWT-based authentication

Add login endpoint and token validation middleware
```
````

## Old patterns section

Keep deprecated approaches out of the main flow without relying on dates in conditions.

```markdown
## Current method

Use the v2 API endpoint: `api.example.com/v2/messages`

## Old patterns

<details>
<summary>Legacy v1 API (deprecated)</summary>

The v1 API used `api.example.com/v1/messages`. This endpoint is no longer supported.
</details>
```
