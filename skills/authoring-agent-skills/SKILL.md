---
name: authoring-agent-skills
description: Writes, reviews, and improves Agent Skills for Claude (SKILL.md files and their bundled reference files and scripts) following Anthropic's official skill authoring best practices. Use when creating a new Skill, editing or auditing an existing SKILL.md, writing a Skill name or description, splitting a Skill into reference files, or checking a Skill before sharing it.
---

# Authoring Agent Skills for Claude

Source of truth: [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices). This Skill condenses that page. When a detail is missing here or may have changed, fetch the Markdown version (append `.md` to the URL) and follow it over this summary.

## Workflow

1. Determine the task:
   - **New Skill** → continue with step 2.
   - **Review or improve an existing Skill** → read every file in the Skill directory, then go to step 5.
2. Identify the gap. Find what Claude gets wrong or lacks without the Skill: project conventions, schemas, fragile sequences, preferred tools. Write only that. See [reference/evaluation.md](reference/evaluation.md) for building evaluations before writing content.
3. Write the frontmatter using the rules in [Frontmatter](#frontmatter).
4. Write the body using [Body rules](#body-rules). Pick structure from [reference/patterns.md](reference/patterns.md). If the Skill bundles scripts or uses MCP tools, read [reference/executable-code.md](reference/executable-code.md).
5. Check the Skill against the [Checklist](#checklist). Fix each failure, then check again. Repeat until every applicable item passes.
6. Report what was created or changed, and list checklist items that could not be verified (typically testing).

## Frontmatter

`name`:

- Maximum 64 characters; lowercase letters, numbers, and hyphens only; no XML tags.
- Must not contain the reserved words `anthropic` or `claude`.
- Prefer gerund form (`processing-pdfs`, `managing-databases`). Noun phrases (`pdf-processing`) and action forms (`process-pdfs`) are acceptable. Match the pattern already used in the Skill collection.
- Avoid vague or generic names: `helper`, `utils`, `tools`, `documents`, `data`.

`description`:

- Non-empty, maximum 1,024 characters, no XML tags.
- Third person only. It is injected into the system prompt: "Processes Excel files", not "I can help you" or "You can use this".
- State both what the Skill does and when to use it, with the key terms a user would actually say. Claude selects among many Skills from this field alone.

```yaml
# Good
description: Extracts text and tables from PDF files, fills forms, and merges documents. Use when working with PDF files or when the user mentions PDFs, forms, or document extraction.

# Bad
description: Helps with documents
```

## Body rules

- **Be concise.** Assume Claude is already capable. Cut anything Claude knows without being told; every paragraph must justify its token cost.
- **Match freedom to fragility.** Prose guidance where many approaches work; templates or parameterized scripts where a preferred pattern exists; exact commands with "do not modify" where the operation is fragile or must be consistent.
- **Keep the `SKILL.md` body under 500 lines.** Move detail into separate files that `SKILL.md` links to directly.
- **Keep references one level deep.** Every bundled file is linked from `SKILL.md`, never only from another reference file. Give reference files longer than 100 lines a table of contents at the top.
- **Give one default, not a menu.** Name the recommended tool or approach and add an escape hatch only for a real exception.
- **Use one term per concept** throughout the Skill.
- **Avoid time-sensitive statements.** Describe the current method; put deprecated approaches in a collapsed "Old patterns" section.
- **Use forward slashes** in all paths, and name files for their content (`form_validation_rules.md`, not `doc2.md`).
- **Add a feedback loop** for quality-critical work: validate → fix → validate again, proceeding only when validation passes.

## Checklist

Copy this checklist and check off items while reviewing:

```
Core quality
- [ ] Description is specific and includes key terms
- [ ] Description says what the Skill does and when to use it, in third person
- [ ] Name is valid: <=64 chars, lowercase/numbers/hyphens, no reserved words
- [ ] SKILL.md body is under 500 lines
- [ ] Additional detail is in separate files (if needed)
- [ ] No time-sensitive information (or it is in an "Old patterns" section)
- [ ] Consistent terminology throughout
- [ ] Examples are concrete, not abstract
- [ ] File references are one level deep
- [ ] Workflows have clear steps

Code and scripts (if any)
- [ ] Scripts handle errors rather than deferring to Claude
- [ ] No unexplained constants
- [ ] Required packages are listed and verified as available
- [ ] No Windows-style paths
- [ ] Validation steps exist for critical operations
- [ ] MCP tools use fully qualified names (ServerName:tool_name)

Testing
- [ ] At least three evaluations created
- [ ] Tested with every model the Skill will run on (Haiku, Sonnet, Opus)
- [ ] Tested with real usage scenarios
```

Do not mark testing items as passed unless the tests were actually run; report them as outstanding instead.
