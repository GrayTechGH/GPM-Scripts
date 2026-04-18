# AGENTS.md

This repository is a reference workspace for writing and reviewing Calibre General Program Mode (GPM) templates.

Agents working here should treat the documents in [`GPM Agent Reference`](./GPM%20Agent%20Reference) as the project authority and keep outputs aligned with them.

## Primary Source Documents

Use these files in this order:

1. [`GPM Agent Reference/GPM Grammar Reference.md`](./GPM%20Agent%20Reference/GPM%20Grammar%20Reference.md)
   Source of truth for GPM syntax, operator precedence, field references, control flow, function definitions, comparisons, truthiness, and return behavior.

2. [`GPM Agent Reference/GPM Programing Rules.md`](./GPM%20Agent%20Reference/GPM%20Programing%20Rules.md)
   Source of truth for code style, caching rules, variable placement, regex hygiene, helper extraction thresholds, formatting, comments, and QA guardrails.

3. [`GPM Agent Reference/GPM Reference Samples.md`](./GPM%20Agent%20Reference/GPM%20Reference%20Samples.md)
   Canonical examples for patterns, idioms, naming, and expected presentation of sample solutions.

4. [`GPM Agent Reference/gpm_function_reference_calibre_8_12.md`](./GPM%20Agent%20Reference/gpm_function_reference_calibre_8_12.md)
   Allow-list and signature reference for built-in functions. Do not invent function names or signatures.

## What This Repo Expects From Agents

- Write Calibre GPM, not Python, JavaScript, or pseudocode, even when code blocks are fenced as `python`.
- Start complete templates with `program:`.
- Keep grammar-correct output strictly aligned to the grammar reference.
- Follow the terminology used in the rulebook. Prefer terms such as `top-cache variable`, `inline-assigned variable`, `long-RHS variable`, `main path`, and `control structure`.
- When giving examples, mirror the style used in the sample compendium.
- When validating functions, use the function reference as the allow-list.

## Authoring Rules

- In multi-line GPM programs, use one statement per line.
- In multi-line GPM programs, end every top-expression with `;` except the last one.
- Put comments on their own lines only. Do not place trailing `#` comments after code.
- Prefer `&` for string concatenation unless a dynamic multi-argument join genuinely calls for `strcat()`.
- Use numeric comparison operators such as `>#` and `==#` when intent is numeric.
- Prefer `inlist_field` for list-valued fields when applicable.
- Extract heavy regexes or long multi-function expressions into named variables.
- Define helper functions only when reuse meets the thresholds in the rulebook.
- Declare helper functions before top-cache variables, and declare callees before callers.

## Working Method

When asked to create, revise, or review GPM:

1. Check grammar and syntax questions against the grammar reference first.
2. Check style, caching, helper extraction, comments, and formatting against the rulebook.
3. Reuse or adapt patterns from the sample compendium when a similar example exists.
4. Confirm every built-in function against the function reference before using it.

## Review Checklist

Before finalizing any GPM output, verify:

- The template is valid GPM and begins with `program:` when appropriate.
- No function names are invented.
- Comments, semicolons, and line structure follow the rulebook.
- Repeated or expensive expressions obey the caching rules.
- Regex usage follows the repo's regex hygiene guidance.
- The final top-level expression returns the intended value.

## Output Conventions

- Present GPM code in fenced code blocks tagged as `python`, because that is the convention used by the reference documents.
- Keep explanations concise and use repo terminology consistently.
- If uncertainty remains after consulting these files, say so explicitly instead of guessing.

## Scope Boundary

- Do not add new project rules, new canonical examples, or undocumented language behavior unless the user explicitly asks for that change.
- Treat the local reference documents as the project authority. External Calibre or MobileRead material can be used only as proposal material, not as silent replacement authority.
