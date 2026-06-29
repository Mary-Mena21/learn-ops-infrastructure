---
argument-hint: "[services-dir] [service-name] [feature-name]"
disable-model-invocation: true
allowed-tools: Bash, Read
description: Trace the full request path for a feature and create a sequence diagram.
---

Parse $ARGUMENTS:
- param1 = first token (directory where services live)
- param2 = second token (service name) - may be absent
- param3 = third token (feature name) - may be absent

**If param2 and param3 are both absent (standalone mode):**

Scan the directory at param1. Identify subdirectories that contain a dependency manifest (package.json, Pipfile, requirements.txt, pyproject.toml, or equivalent). Each such subdirectory is a service.

List every service you found, numbered starting at 1.
Then ask: "Which service should I trace? Enter a number:"
Wait for the user to reply before continuing. The user's choice becomes param2.

Then scan param1/param2 for routes and named features. Look in URL config files, routers, controllers, viewsets, or equivalent files.

List every route or named feature you found, numbered starting at 1.
Then ask: "Which feature should I trace in the next phase? Enter a number:"
Wait for the user to reply before continuing. The user's choice becomes param3.

---

**Trace param3 in the service at param1/param2:**

Read the actual source files. Fill in the table below with the specific file path, class or function name, and a one-sentence description for each layer. Note any database queries.

# Trace Notes (AI): param3 (param2)

## Request Path

| Layer | File | Class / Function | What it does |
|-------|------|-----------------|--------------|
| UI dialog | | | |
| API helper | | | |
| URL router | | | |
| View | | | |
| Serializer | | | |
| DB | | | |
| UI refresh | | | |

## Sequence Diagram

Generate a Mermaid sequence diagram using the layers above as participants. Each interaction is a labeled arrow using the HTTP verb or method name. Database queries appear as separate interactions. Return values use -->> arrows.

## Output

Write to file observability-ai-{param1}-{param2}-{param3}.md

The trace table goes under section "### Request path table from Claude"

The mermaid sequence diagram should be embedded under the title "### Sequence Diagram"