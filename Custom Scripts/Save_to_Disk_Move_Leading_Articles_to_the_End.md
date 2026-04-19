# Save to Disk - Move Leading Articles to the End

Use this in a Save to Disk template when you want leading articles moved to the end in both the series folder and the title.

Examples:

- `The Expanse` -> `Expanse, The`
- `An Ember in the Ashes` -> `Ember in the Ashes, An`
- `A Study in Scarlet` -> `Study in Scarlet, A`

```python
program:
  def move_article(s):
    return re(s, '^(?i)(the|an|a)\s+(.*)$', '\2, \1')
  fed;

  ser = field('series');
  tit = raw_field('title');
  ser2 = move_article(ser);
  tit2 = move_article(tit);
  if ser then
    ser2 & '/' & tit2
  else
    tit2
  fi
```

Notes:

- The helper is reused for both `series` and `title`.
- `raw_field('title')` keeps the original title text instead of applying title formatting.
- The regex requires a space after the article, so names like `A.I.` are not treated as article-prefixed titles.
- Calibre will still sanitize filesystem-illegal characters after this template runs.
