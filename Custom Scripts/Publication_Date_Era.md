# Publication Date -> Era

Use this in a text custom column when you want a sortable era label based on `pubdate`.

Output:

- Empty `pubdate` -> `''`
- Before 1900 -> `01 cen.`, `02 cen.`, ... `19 cen.`
- 1900 and later -> `1900s`, `1910s`, ... `2020s`

```python
program:
  if p = field('pubdate') then
    y = format_date(p, 'yyyy');
    if y <# 1900 then
      c = floor((y - 1) / 100) + 1;
      zwj = '‍';
      zwj & substr('0' & c, -2, 0) & ' cen.'
    else
      d = floor(y / 10) * 10;
      d & 's'
    fi
  fi
```

Notes:

- Uses `format_date()` to extract the numeric year.
- Centuries are padded to two digits for lexical sorting.
- The invisible zero-width joiner before century labels keeps them sorting before decade labels.
