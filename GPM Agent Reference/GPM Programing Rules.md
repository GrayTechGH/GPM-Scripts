# Rulebook: GPM Programming Ruleset (Clean)

Slug: gpm-rulebook-clean

Version: 2.0.5 Updated: 2025-09-17

Status: Draft

---

## 1) Purpose

A concise, non-redundant rule set for writing clear, efficient, and correct Calibre **General Program Mode (GPM)** templates—optimized for maintainability and predictable behavior.

## 2) Scope & Terms

- **Applies to:** Calibre GPM templates.
- **Control structure:** One `if/elif/else` chain or a `for` loop.
- **Main path:** Top-level code outside any control structure.
- **Heavy/long RHS:** Regex-heavy or multi-function expressions where readability/performance benefits from extracting to a variable.

## 3) Caching Rule (cache vs inline)

**Goal:** avoid redundant evaluation while keeping programs readable.

**Create a top-cache variable near the start—after helper declarations** when a value is:

1. used on the **main path**, or
2. referenced in **more than one control structure** (e.g., two distinct `if` chains or multiple `for` loops).

**Use an inline-assigned variable locally** when a value is used **only within a single control structure** and appears **both in its condition and body** (i.e., referenced ≥2× on that one executed path). Prefer the idiom: `if x = expr then …`.

**Inline** when an expression is referenced **only once** on the executed path.
           
**Create a long-RHS variable** if the RHS is a **heavy regex** or a **multi-function chain**, whether it’s **single-use** or **reused across branches/structures** (readability now; enables safe reuse later).

**Counting rule:** Count references **per execution path**. Do **not** add counts across mutually exclusive branches of the **same** `if/elif/else` chain. Cross-structure reuse **does** trigger top-caching.

## 4) Variable Placement & Naming

- Place **top-cache variables** at the top of the program, immediately after helper function declarations.
- Use **inline-assigned variables** only when the value is confined to one control structure (condition + body) and nowhere else.
- Prefer short mnemonic names for short-lived locals; semantic names for cross-structure/top-cached variables.

## 5) Branching & Comparison Discipline

- Use equality/ordering operators when they express intent; use regex only for pattern matching needs.
- For numeric intent, use the numeric comparison operators (the `#` forms).
- For list-valued fields, prefer `inlist_field` for correctness and performance.

## 6) Regex Hygiene

- Escape metacharacters when interpolating metadata into regex patterns.
- Anchor when you need exact/boundary-constrained matches; normalize variants (e.g., NBSP → space) **before** matching if relevant.
- Keep complex regexes in **named variables** (long-RHS variable rule) for readability.

## 7) Loops & Rendering

- Use `for … in … : … rof` for iteration; use `separator` to control join punctuation inside the loop.
- Inside loops: `break` exits the loop; `continue` skips to next iteration; `return` ends the **entire program**.

## 8) String & Path Construction

- Build paths with explicit separators (`'/'`).
- Prefer the `&` operator for string concatenation instead of the `strcat()` function, unless you’re joining a dynamic number of arguments.
- Sanitize/normalize metadata that might contain separators or illegal filesystem characters **before** concatenation.
- Defer expensive transforms (e.g., article shifting, multi-`re()` cleanup) to a **single variable** per the long-RHS variable rule.

## 9) Helper Functions (`def`/`call`)

- **Reuse threshold:** Define a helper **only** when the logic is reused **≥2×**. Do **not** create single-use helpers—use a variable instead (top-cache, inline-assigned, or long-RHS variable).
- Extract repeated logic when:
  - **2-line snippet** is reused ≥4 times.
  - **3-line snippet** is reused ≥3 times.
  - **≥4-line snippet** is reused ≥2 times.
- **Declaration order:** Place all helper functions **before** any top-cache variables. Within helpers, ensure any callee is declared earlier than its caller. Consolidate or refactor if mutual dependencies arise.
- Prefer pure helpers (return a value; avoid hidden dependencies).
- Use `return` to end function evaluation early when appropriate.
- **Note:** Variants of the same multi-step pipeline (same operations, different input fields/vars) count as reuse; parameterize and extract.

## 10) Performance Hints

- Apply the caching rule to reduce repeated `field()`/`re()`.
- Prefer `inlist_field` over manual splits for multi-valued custom columns.
- Keep conditions light; shift heavy work into a single variable evaluated at most once.

## 11) Commenting & Formatting

- One obvious action per line; indent for structure; end statements with `;` when sequencing.
- **Comments must be on their own line.** Trailing comments on code lines are invalid in GPM.
- **One statement per line.** Don’t chain statements with `;` on the same line.
- **Semicolons in multi-line programs.** In multi-line programs, every top-expression ends with `;`—except the last one. The only exception is a single-expression program written on one line.
- Keep lines short; extract long chains to named variables (long-RHS variable rule).

**Good**

```python
# set a from b
a = b;
# prepare b
b = c;
```

**Bad**

```python
a = b; # set a       # invalid in GPM
a = b; b = c;        # valid but not permitted by our style
```

## 12) Pre-flight Checklist

1. Caching: Cross-structure/main-path values top-cached; single-structure condition+body values inline-assigned; true single-use values inlined.
2. Long RHS: Heavy regex or multi-function RHS extracted to a named variable.
3. Per-path counting: No cross-branch counting within the same if/elif/else chain; count uses per executed path.
4. Branch order: Most selective tests first; else is a true fallback.
5. Paths: Use explicit '/' and sanitize metadata that may contain separators or illegal filesystem characters before concatenation.
6. Transforms: Expensive transforms (article shift, regex cleanup) evaluated once and reused.
7. Loops: Use separator to join; confirm any break/return won't skip required work.
8. Early exit: Any return is intentional and safe.
9. Final value: The last top-level expression returns what you expect; remove any debug() scaffolding.
10. No trailing `#` after a statement; comments must be on their own lines.
11. One statement per line (no `...; ...` on the same line).
12. All functions used are from the official Function Reference; no invented function names.
13. **Declaration order:** Helpers declared before top-cache variables; within helpers, callees declared before callers (no cycles).
14. **Semicolons in multi-line programs:** Every top-expression except the last must end with `;`.

## 13) Meta Rules

1. **Code block style** Always show GPM code in fenced code blocks tagged as `python`.

   Example:

   ```python
   program:
     if field('series') then
       field('series')
     else
       field('title')
     fi
   ```

2. **Source-of-truth discipline** *Default:* use the user’s uploaded documents (rulebook, grammar, function reference, sample compendium) as the sole authority. *When in doubt:* consult the official [Calibre manual](https://manual.calibre-ebook.com/) and [MobileRead forums](https://www.mobileread.com/) **only as sources for proposals**. Be explicit about confidence and ask if the user wants the proposal added.

3. **No unsolicited insertions** Do not add new rules, examples, or code into a canvas unless the user explicitly asks.

   - If a canvas already contains code, preserve it during edits/reviews.
   - In chat replies, code examples are fine.

4. **Terminology consistency** Use the same terminology everywhere — in canvases, chat explanations, and examples.

   - Always prefer the terms defined in the Rulebook (e.g., *top-cache variable*, *inline-assigned variable*, *long-RHS variable*).
   - Do not substitute synonyms (e.g., don’t swap *field* ↔ *column*, or *inline-assign* ↔ *temporary assignment*).
   - Apply consistency to **all defined terms**, not just variables (e.g., *main path*, *control structure*, *branch*, *regex hygiene*).
   - Generic terms like *variable* or *value* may be used freely, as long as they don’t introduce ambiguity.

## Appendix: Lint & QA (Machine-checkable guardrails)

- **Trailing inline comment (invalid)**

  - **Pattern:** `;[ 	]*#`
  - **Guidance:** Split into a statement line and a separate comment line above.
  - **Severity:** **Error**

- **Multiple statements on one line (style violation)**

  - **Pattern:** `;[ 	]*\S`
  - **Guidance:** Split into multiple lines at semicolons.
  - **Severity:** **Style fail**

- **Unknown function call (potential invented function)**

  - **Detection:** Identifier followed by `(` that is **not** in the GPM Function Reference allow‑list.
    - Two‑step check: (1) find candidates with `\b[A-Za-z_]\w*\s*\(`; (2) filter out allow‑listed function names.
  - **Guidance:** Fail lint; confirm in the Function Reference; replace with the correct function if needed.
  - **Severity:** **Error**

## Change Log

- **2025-09-17** — Initial clean rulebook (no examples).
- **2025-09-19** — Revised helper function rules; removed tiny inline idioms note.
- **2025-09-20** — Added Meta Rules; updated terminology to “variable.”
- **2025-09-21** — Added checklist for valid functions; grouped lint guardrails.
- **2025-09-21** — Clarified order: helpers before top-cache vars; updated §3, §4.
- **2026-04-17** — Edit typos in examples and transcription errors
- 
