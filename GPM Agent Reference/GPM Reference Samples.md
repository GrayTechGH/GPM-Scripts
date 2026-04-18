# GPM Sample Compendium

**Document Slugs:** gpm-reference-samples, gpm-samples, gpm-examples, gpm-snippets



**Preface**\
This compendium is designed for both human readers and AI code generators. Each example includes:

- A plain-English description so humans can understand what the program does.
- A slug identifier, inputs, and outputs so AIs can reference and adapt examples programmatically.
- Code blocks fenced as `python` but containing GPM code inside, to ensure clarity.

Some examples use custom fields (e.g., `#genre`, `#mybool`). When writing your own code, don’t assume those fields exist unless you’ve created them. Default to built-in fields instead.

## Table of Contents

- [If Variants](#if-variants)
  - [if (slug: if-basic)](#if-slug-if-basic)
  - [Nested if (slug: if-nested-mybool)](#nested-if-slug-if-nested-mybool)
  - [All of these are equivalent (slug: if-equivalents)](#all-of-these-are-equivalent-slug-if-equivalents)
- [Series or Title](#series-or-title)
- [Normalize Genres After the First Dot](#normalize-genres-after-the-first-dot)
- [Approximate Duration](#approximate-duration)
- [Publication Date → Era](#publication-date--era)
- [Detect Author Surname in Publisher](#detect-author-surname-in-publisher)
- [Relational & Logical Examples](#relational--logical-examples)
- [Save to Disk — Move Leading Articles to the End](#save-to-disk--move-leading-articles-to-the-end)

---

## If Variants

### if (slug: if-basic)

**Inputs:** series field\
**Output:** "yes" if present, "no" otherwise

```python
program:
  if field('series') then 'yes' else 'no' fi
```

Block form that also shows branching into two variables, then concatenates them:

```python
program:
  if field('series') then
      a = 'yes';
      b = 'no'
  else
      a = 'no';
      b = 'yes'
  fi;
  strcat(a, '-', b)
```

---

### Nested if (slug: if-nested-mybool)

**Inputs:** series field, custom column `#mybool` (yes/no)\
**Output:** "yes", "no", or "no series"

```python
program:
  if field('series') then
    if check_yes_no(field('#mybool'), '', '', '1') then
      'yes'
    else
      'no'
    fi
  else
    'no series'
  fi
```

---

### All of these are equivalent (slug: if-equivalents)

**Inputs:** series field\
**Output:** "foo" if present, "bar" otherwise

```python
program: if field('series') then 'foo' else 'bar' fi
```

```python
program:
  if field('series') then a = 'foo' else a = 'bar' fi; a
```

```python
program:
  a = if field('series') then 'foo' else 'bar' fi; a
```

---

## Series or Title

### series-or-title (slug: series-or-title)

**Inputs:** `series`, `title`\
**Output:** the `series` value if present, otherwise the `title` value

```python
program:
  if field('series') then field('series') else field('title') fi
```

---

## Normalize Genres After the First Dot

### genre-normalize-after-dot (slug: genre-normalize-after-dot)

Extract the substring after the first dot in each `#genre` entry, de-duplicate, sort, and return as a comma-separated list.

**Inputs:** list-valued custom column `#genre` (items like `foo.bar`)\
**Output:** comma-separated string of unique normalized genres

```python
program:
  tags = '';
  for i in $#genre:
    j = re(i, '^.*?\.(.*)$', '\1');
    tags = add(tags, j)
  rof;
  merge(sort(unique(tags), 'asc'), ', ', '1')
```

---

\
## Publication Date → Era

### pubdate-to-era (slug: pubdate-to-era)

Convert a book’s publication date into a century or decade label suitable for lexical sorting.

**Inputs:** `pubdate`\
**Output:**

- *Empty* → `''`
- *Before 1900* → century (`01 cen.`, `02 cen.`, … `19 cen.`)
- *1900 +* → decade (`1900s`, `1910s`, … `2020s`)
- *Centuries* are prefixed with an invisible zero-width joiner (`\u200d`) to ensure they sort before decades.

```python
program:
  if (p = field('pubdate')) != '' then
    y = format_date(p, 'yyyy');

    if y <# 1900 then
      c = floor((y - 1) / 100) + 1;
      # \u200d added for sorting before digits
      ' ‍' & substr('0' & c, -2, 0) & ' cen.'
    else
      d = floor(y / 10) * 10;
      d & 's'
    fi
  fi
```

**Notes**

- Uses `format_date()` for numeric year extraction.
- Pads centuries to two digits for clean lexical alignment.
- `\u200d` (zero-width joiner) makes all centuries sort together before any decade strings.

---

## Detect Author Surname in Publisher

### detect-author-surname-in-publisher (slug: detect-author-surname-in-publisher)

**Inputs:** `authors`, `author_sort`, `publisher`\
**Output:** `'1'` if the best-guess surname of the first listed author appears in `publisher` as a full word; otherwise empty

```python
program:
first_author = re(field('authors'), '^(.*?)\s*(?:&|$).*', '\1');

# usually Last, First
sort_first_author = re(field('author_sort'), '^(.*?)\s*(?:&|$).*', '\1');

# surname candidates
sort_surname   = re(sort_first_author, '^\s*([^,]+).*', '\1');
author_surname = re(first_author, '^.*\s+(\S+)\s*$', '\1');

lname = if ',' in sort_first_author then sort_surname
        elif strlen(author_surname) ># 0 then author_surname
        else first_author fi;

# pattern with safe boundaries and escaped metachars
pat   = strcat('(?i)(?<!\\w)', re(lname, '([.^$*+?()\[\]{}|\\])', '\\\\$1'), '(?!\\w)');

# normalize NBSP to space and test
strlen(lname) ># 0 && pat in re(field('publisher'), '\u00A0', ' ')
```

**Notes**

- Picks a surname by preferring the `author_sort` left side (before the comma), then falling back to the last token of `authors`.
- Escapes all regex metacharacters in the candidate surname before searching.
- Uses `(?<!\\w)` / `(?!\\w)` instead of `\\b` to avoid edge cases with non-ASCII or underscores.
- Normalizes non-breaking spaces in `publisher` to ordinary spaces before testing.
- Returns `'1'` or empty; adapt to your workflow (e.g., wrap in `if ... then 'self' fi`).

---

## Relational & Logical Examples

### relational-and-logical-examples (slug: relational-and-logical-examples)

**String equality**

```python
program: field('series') == 'foo'
```

**Regex match on a field**

```python
program: 'f.o' in field('series')
```

**Regex membership in a list-valued field**

```python
program: 'science' inlist $#genre
```

**Exact match with anchors**

```python
program: '^science$' inlist $#genre
```

**Regex membership in authors string**

```python
program: 'asimov' inlist $authors
```

**Regex membership using field semantics**

```python
program: 'asimov' inlist_field 'authors'
```

**Ends-with match on author**

```python
program: 'asimov$' inlist_field 'authors'
```

**Inequality + ternary style**

```python
program: if field('series') != 'foo' then 'bar' else 'mumble' fi
```

**OR with two explicit equals**

```python
program: if field('series') == 'foo' || field('series') == '1632' then 'yes' else 'no' fi
```

**Equivalent using regex**

```python
program: if '^(foo|1632)$' in field('series') then 'yes' else 'no' fi
```

**Lexical vs numeric comparison**

```python
program: if 11 > 2 then 'yes' else 'no' fi
```

```python
program: if 11 ># 2 then 'yes' else 'no' fi
```

---

## Save to Disk — Move Leading Articles to the End

### move-articles-series-and-title (slug: move-articles-series-and-title)

**Goal:** For save-to-disk paths, move an initial `The / A / An` in both series-folder and title to the end (e.g., `The Expanse` → `Expanse, The`).\
**Inputs:** `series`, raw `title`\
**Output:** `Series, The/Title, The` (if series exists) otherwise `Title, The`

```python
program:
  def move_article(s):
    # If s starts with The/An/A + space, move that article to the end as ", Article"
    return re(s, '^(?i)(the|an|a)\s+(.*)$', '\2, \1')
  fed;

  ser = field('series');
  tit = raw_field('title');

  ser2 = move_article(ser);
  tit2 = move_article(tit);

  if ser then ser2 & '/' & tit2 else tit2 fi
```

**Notes**

- Case-insensitive and requires a **space** after the article, so `A.I.` won’t be misread as the article `A`.
- Uses `raw_field('title')` to avoid any automatic title formatting.
- Calibre’s save-to-disk will sanitize illegal filesystem characters separately; this template focuses only on article movement.

