# Combine Goodreads + Romance.io

Use this in the `#Tropes` template column.

Assumed lookup names:

- `#romanceio`
- `#goodreads`
- `#tropes`

If your actual lookup names differ, replace them in the template.

```python
program:
  def map_romanceio_tag(tag, map_blob):
    clean_tag = lowercase(re(tag, '^\s+|\s+$', ''));
    escaped_tag = re(clean_tag, '([.^$*+?()\[\]{}|\\])', '\\\\$1');
    pattern = '(^|\\|\\|)' & escaped_tag & '=>([^|]+)(\\|\\||$)';
    return contains(
      map_blob,
      pattern,
      re(map_blob, '.*' & pattern & '.*', '\2'),
      ''
    )
  fed;

  romanceio_map = globals(romanceio_trope_map='');

  if !romanceio_map then
    romanceio_map =
      'man/woman=>OW/OM' &
      '||woman/man=>OW/OM' &
      '||group hockey=>Sports' &
      '||sports=>Sports' &
      '||athlete=>Sports' &
      '||boss/employee=>Workplace, Power imbalance' &
      '||workplace=>Workplace' &
      '||office romance=>Workplace' &
      '||friends to lovers=>Friends to lovers' &
      '||second chance=>Second chance' &
      '||grumpy/sunshine=>Grumpy sunshine' &
      '||fake dating=>Fake dating' &
      '||marriage of convenience=>Marriage of convenience' &
      '||forced proximity=>Forced proximity' &
      '||small town=>Small town';
    set_globals(romanceio_trope_map=romanceio_map)
  fi;

  goodreads_tags = field('#goodreads');
  romanceio_tags = '';

  for romanceio_tag in $#romanceio:
    mapped_tags = map_romanceio_tag(romanceio_tag, romanceio_map);
    if mapped_tags then
      romanceio_tags = list_union(romanceio_tags, mapped_tags, ',')
    fi
  rof;

  list_union(goodreads_tags, romanceio_tags, ',')
```

Notes:

- `#Goodreads` is treated as already premapped and is passed through as-is.
- `#Romanceio` tags not found in the dictionary are ignored.
- The Romance.io dictionary lives in the `romanceio_trope_map` global and is only regenerated when that global is empty.
- `list_union()` removes duplicate tags, including duplicates returned by multi-tag dictionary values.
