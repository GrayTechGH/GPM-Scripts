# Detect Author Surname in Publisher

Use this in a Yes/No-style text custom column when you want to flag books where the first author's surname appears in `publisher`.

Output:

- Returns `'1'` when a surname match is found.
- Returns `''` when no match is found.

```python
program:
  authors_value = field('authors');
  author_sort_value = field('author_sort');
  publisher_value = re(field('publisher'), '\u00A0', ' ');
  first_author = re(authors_value, '^(.*?)\s*(?:&|$).*', '\1');
  sort_first_author = re(author_sort_value, '^(.*?)\s*(?:&|$).*', '\1');
  sort_surname = re(sort_first_author, '^\s*([^,]+).*', '\1');
  author_surname = re(first_author, '^.*\s+(\S+)\s*$', '\1');
  lname =
    if ',' in sort_first_author then
      sort_surname
    elif strlen(author_surname) ># 0 then
      author_surname
    else
      first_author
    fi;
  escaped_lname = re(lname, '([.^$*+?()\[\]{}|\\])', '\\\\$1');
  pat = '(?i)(?<!\\w)' & escaped_lname & '(?!\\w)';
  if strlen(lname) ># 0 && pat in publisher_value then
    '1'
  fi
```

Notes:

- Prefers the left side of `author_sort` when it looks like `Last, First`.
- Falls back to the last token of the first listed author in `authors`.
- Escapes regex metacharacters before testing the surname.
- Normalizes non-breaking spaces in `publisher` before matching.
