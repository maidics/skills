# Evaluating and iterating on a Skill

Condensed from [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices#evaluation-and-iteration).

## Build evaluations first

Write evaluations before extensive instructions so the Skill addresses observed failures, not imagined ones.

1. **Identify gaps**: run Claude on representative tasks without the Skill and record specific failures or missing context.
2. **Create evaluations**: build at least three scenarios that test those gaps.
3. **Establish a baseline**: measure performance without the Skill.
4. **Write minimal instructions**: just enough to close the gaps.
5. **Iterate**: rerun, compare against the baseline, refine.

There is no built-in evaluation runner; store scenarios in a simple structure and run them however the project allows:

```json
{
  "skills": ["pdf-processing"],
  "query": "Extract all text from this PDF file and save it to output.txt",
  "files": ["test-files/document.pdf"],
  "expected_behavior": [
    "Reads the PDF using an appropriate library or command-line tool",
    "Extracts text from all pages without missing any",
    "Saves the extracted text to output.txt in a readable format"
  ]
}
```

## Author with one instance, test with another

- **Authoring instance**: helps design and refine the Skill. Capture the context the user had to supply repeatedly during a real task (table names, filtering rules, conventions) and turn that into the Skill.
- **Testing instance**: a fresh session with the Skill loaded, given real tasks rather than contrived ones.

Loop: observe where the testing instance struggles → bring the specific observation back ("it forgot to filter test accounts on the regional report") → adjust prominence, wording, or structure → test again.

## What to watch for

| Observation | Likely fix |
| --- | --- |
| Skill does not trigger when expected | Sharpen `description`: add the terms users actually say and the "use when" contexts |
| Files read in an unexpected order | Restructure; the layout is less intuitive than assumed |
| A linked file is never followed | Make the link more explicit or prominent in `SKILL.md` |
| The same reference file is read every time | Move that content into `SKILL.md` |
| A bundled file is never read | Remove it or signal it better |
| A stated rule is skipped | Move it earlier or use stronger wording ("MUST filter") |

## Test across models

Test with every model the Skill will run on. Smaller models may need more guidance; larger models are hurt by over-explanation. Aim for instructions that work for all of them.
