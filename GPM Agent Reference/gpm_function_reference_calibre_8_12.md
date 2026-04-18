# Reference for all built-in GPM language functions — calibre 8.12.0 documentation

### Key

Format used for each function:
**name —** *arg count range* **—** `signature_verbatim` **—** DESCRIPTION [#anchor_id]

- **Arg count range:**

  - “n arguments” = exactly n
  - “n–m arguments” = between n and m
  - “n–infinite arguments” = n or more (variadic)

- **Signature notation:**

  - `[...]` — optional argument or group
  - `[*]` suffix inside brackets — the bracketed part is repeatable zero or more times (e.g., \[arg]\*)
  - `[+]` suffix inside brackets — the bracketed part is repeatable one or more times (e.g., \[patternN, valueN,]+)
  - Commas in `(...)` are separators
  - Spaces around `() [] , *` are not significant


## Arithmetic

**add —** *1–infinite arguments* — `add(x [, y\*])` — Sum of arguments; errors on non-number. Usually use + operator. [#add]

**ceiling —** *1 argument* — `ceiling(value)` — Smallest integer ≥ value; error if not a number. [#ceiling]

**divide —** *2 arguments* — `divide(x, y)` — Returns x/y; errors if x or y not numbers; usually use /. [#divide]

**floor —** *1 argument* — `floor(value)` — Largest integer ≤ value; error if value not a number. [#floor]

**fractional_part —** *1 argument* — `fractional_part(value)` — Returns digits after decimal; error if value not a number. [#fractional-part]

**mod —** *2 arguments* — `mod(value, y)` — Floor of remainder value/y; error if operands not numbers. [#mod]

**multiply —** *1–infinite arguments* — `multiply(x [, y\*])` — Product of arguments; error if any not number; usually use *. [#multiply]

**round —** *1 argument* — `round(value)` — Nearest integer to value; error if value not a number. [#round]

**subtract —** *2 arguments* — `subtract(x, y)` — Returns x - y; error if operands not numbers; usually use -. [#subtract]


## Boolean

**and —** *1–infinite arguments* — `and(value [, value\*])` — Returns the string '1' if all values are not empty, otherwise returns the empty string. You can have as many values as you want. In most cases you can use the && operator instead of this function. One reason not to replace and() with && is when short-circuiting can change the results because of side effects. For example, and(a='',b=5) will always do both assignments, where the && operator won’t do the second. [#and]

**not —** *1 argument* — `not(value)` — Returns the string '1' if the value is empty, otherwise returns the empty string. This function can usually be replaced with the unary not (!) operator. [#not]

**or —** *1–infinite arguments* — `or(value [, value\*])` — Returns the string '1' if any value is not empty, otherwise returns the empty string. You can have as many values as you want. This function can usually be replaced by the || operator. A reason it cannot be replaced is if short-circuiting will change the results because of side effects. [#or]


## Case changes

**capitalize —** *1 argument* — `capitalize(value)` — Returns the value with the first letter in upper case and the rest lower case. [#capitalize]

**lowercase —** *1 argument* — `lowercase(value)` — Returns the value in lower case. [#lowercase]

**titlecase —** *1 argument* — `titlecase(value)` — Returns the value in title case. [#titlecase]

**uppercase —** *1 argument* — `uppercase(value)` — Returns the value in upper case. [#uppercase]


## Database functions

**annotation_count —** *0 arguments* — `annotation_count()` — Return the total number of annotations of all types attached to the current book. [#annotation-count]

**approximate_formats —** *0 arguments* — `approximate_formats()` — Return a comma-separated list of formats associated with the book. [#approximate-formats]

**book_count —** *2 arguments* — `book_count(query, use_vl)` — Returns the count of books found by searching for query. [#book-count]

**book_values —** *4 arguments* — `book_values(column, query, sep, use_vl)` — Returns list unique values contained column column separated books found searching query. [#book-values]

**extra_file_modtime —** *2 arguments* — `extra_file_modtime(file_name, format_string)` — Returns the modification time of the extra file file_name in the book’s data/ folder if it exists, otherwise -1. [#extra-file-modtime]

**extra_file_names —** *1 argument* — `extra_file_names(sep [, pattern])` — Returns a sep-separated list of extra files in the book’s data/ folder. [#extra-file-names]

**extra_file_size —** *1 argument* — `extra_file_size(file_name)` — Returns the size in bytes of the extra file file_name in the book’s data/ folder if it exists, otherwise -1. [#extra-file-size]

**formats_modtimes —** *1 argument* — `formats_modtimes(date_format_string)` — Return a comma-separated list of colon-separated items FMT:DATE representing modification times for the formats of a book. [#formats-modtimes]

**formats_path_segments —** *5 arguments* — `formats_path_segments(with_author, with_title, with_format, with_ext, sep)` — Return parts of the path to a book format in the calibre library separated by sep. [#formats-path-segments]

**formats_paths —** *0–1 arguments* — `formats_paths([separator])` — Return a separator-separated list of colon-separated items FMT:PATH giving the full path to the formats of a book. [#formats-paths]

**formats_sizes —** *0 arguments* — `formats_sizes()` — Return a comma-separated list of colon-separated FMT:SIZE items giving the sizes of the formats of a book in bytes. [#formats-sizes]

**get_link —** *2 arguments* — `get_link(field_name, field_value)` — Fetch the link for field field_name with value field_value. [#get-link]

**get_note —** *3 arguments* — `get_note(field_name, field_value, plain_text)` — Fetch the note for field field_name with value field_value. [#get-note]

**has_extra_files —** *0–1 arguments* — `has_extra_files([pattern])` — Returns the count of extra files, otherwise ‘’ . [#has-extra-files]

**has_note —** *2 arguments* — `has_note(field_name, field_value)` — . Check if a field has a note. This function has two variants: [#has-note]


## Date functions

**date_arithmetic —** *3 arguments* — `date_arithmetic(value, calc_spec, fmt)` — Calculate a new date from value using calc_spec. [#date-arithmetic]

**days_between —** *2 arguments* — `days_between(date1, date2)` — Return the number of days between date1 and date2. [#days-between]

**today —** *0 arguments* — `today()` — Return a date+time string for today . [#today]


## Formatting values

**f_string —** *1 argument* — `f_string(string)` — Interpret string similar to how python interprets f strings. [#f-string]

**finish_formatting —** *4 arguments* — `finish_formatting(value, format, prefix, suffix)` — Apply format, prefix, suffix value same done template like {series_index:05.2f| [#finish-formatting]

**format_date —** *2 arguments* — `format_date(value, format_string)` — Format the value, which must be a date string, using the format_string, returning a string. [#format-date]

**format_date_field —** *2 arguments* — `format_date_field(field_name, format_string)` — Format value field field_name, which must lookup name date field, either standard custom. [#format-date-field]

**format_duration —** *2–3 arguments* — `format_duration(value, template, [largest_unit])` — Format the value, a number of seconds, into a string showing weeks, days, hours, minutes, and seconds. [#format-duration]

**format_number —** *2 arguments* — `format_number(value, template)` — Interprets value number formats that number using Python formatting template such {0:5.2f} {0:,d} ${0:5,.2f}. [#format-number]

**human_readable —** *1 argument* — `human_readable(value)` — Expects the value to be a number and returns a string representing that number in KB, MB, GB, etc. [#human-readable]

**rating_to_stars —** *2 arguments* — `rating_to_stars(value, use_half_stars)` — Returns the value as string of star  characters. [#rating-to-stars]


## GUI functions

**selected_books —** *0–2 arguments* — `selected_books([sorted_by, ascending])` — Returns a list of book ids in selection order for the currently selected books. [#selected-books]

**selected_column —** *0 arguments* — `selected_column()` — Returns the lookup name of the column containing the currently selected cell. [#selected-column]

**show_dialog —** *1 argument* — `show_dialog(html_or_text)` — Show a dialog containing the html or text. [#show-dialog]

**sort_book_ids —** *3 arguments* — `sort_book_ids(book_ids, sorted_by, ascending [, sorted_by, ascending\*])` — Returns list book sorted column specified lookup name sorted_by order specified ascending. [#sort-book-ids]


## Get values from metadata

**author_links —** *2 arguments* — `author_links(val_separator, pair_separator)` — Returns string containing list authors those authors’ link values form: author1 val_separator author1_link pair_separator author2 val_separator author2_link [#author-links]

**author_sorts —** *1 argument* — `author_sorts(val_separator)` — Returns a string containing a list of author’s sort values for the authors of the book. [#author-sorts]

**booksize —** *0 arguments* — `booksize()` — Returns the value of the calibre size field. [#booksize]

**connected_device_name —** *1 argument* — `connected_device_name(storage_location_key)` — If a device is connected then return the device name, otherwise return the empty string. [#connected-device-name]

**connected_device_uuid —** *1 argument* — `connected_device_uuid(storage_location_key)` — If a device is connected then return the device uuid , otherwise return the empty string. [#connected-device-uuid]

**current_library_name —** *0 arguments* — `current_library_name()` — Return the last name on the path to the current calibre library. [#current-library-name]

**current_library_path —** *0 arguments* — `current_library_path()` — Return the full path to the current calibre library. [#current-library-path]

**current_virtual_library_name —** *0 arguments* — `current_virtual_library_name()` — Return the name of the current virtual library if there is one, otherwise the empty string. [#current-virtual-library-name]

**field —** *1 argument* — `field(lookup_name)` — Returns the value of the metadata field with lookup name lookup_name. [#field]

**has_cover —** *0 arguments* — `has_cover()` — Return 'Yes' if the book has a cover, otherwise the empty string. [#has-cover]

**is_marked —** *0 arguments* — `is_marked()` — Check whether the book is marked in calibre. [#is-marked]

**language_codes —** *1 argument* — `language_codes(lang_strings)` — Return the language codes for the language names passed in lang_strings. [#language-codes]

**language_strings —** *2 arguments* — `language_strings(value, localize)` — Return the language names for the language codes  passed in value. [#language-strings]

**ondevice —** *0 arguments* — `ondevice()` — Return the string 'Yes' if ondevice is set, otherwise return the empty string. [#ondevice]

**raw_field —** *1 argument* — `raw_field(lookup_name [, optional_default])` — Returns the metadata field named by lookup_name without applying any formatting. [#raw-field]

**raw_list —** *2 arguments* — `raw_list(lookup_name, separator)` — Returns the metadata list named by lookup_name without applying any formatting or sorting, with the items separated by separator. [#raw-list]

**series_sort —** *0 arguments* — `series_sort()` — Returns the series sort value. [#series-sort]

**user_categories —** *0 arguments* — `user_categories()` — Return a comma-separated list of the user categories that contain this book. [#user-categories]

**virtual_libraries —** *0 arguments* — `virtual_libraries()` — Return a comma-separated list of Virtual libraries that contain this book. [#virtual-libraries]


## Iterate over values

**first_non_empty —** *1–infinite arguments* — `first_non_empty(value [, value\*])` — Returns the first value that is not empty. [#first-non-empty]

**lookup —** *2 arguments* — `lookup(value, [ pattern, key, ]\* else_key)` — The patterns will be checked against the value in order. [#lookup]

**switch —** *2 arguments* — `switch(value, [patternN, valueN,]+ else_value)` — each patternN, valueN pair, checks if value matches regular expression patternN if returns associated valueN. [#switch]

**switch_if —** *1 argument* — `switch_if([test_expression, value_expression,]+ else_expression)` — For each test_expression, value_expression pair, checks if test_expression is True  and if so returns the result of value_expression. [#switch-if]


## List lookup

**identifier_in_list —** *2 arguments* — `identifier_in_list(val, id_name [, found_val, not_found_val])` — Treat val as a list of identifiers separated by commas. [#ff-identifier-in-list]

**list_contains —** *3 arguments* — `list_contains(value, separator, [ pattern, found_val, ]\* not_found_val)` — Interpret the value as a list of items separated by separator, checking the pattern against each item in the list. [#list-contains]

**list_item —** *3 arguments* — `list_item(value, index, separator)` — Interpret the value as a list of items separated by separator, returning the ‘index’th item. [#list-item]

**select —** *2 arguments* — `select(value, key)` — Interpret the value as a comma-separated list of items with each item having the form id:id_value . [#select]

**str_in_list —** *3 arguments* — `str_in_list(value, separator, [ string, found_val, ]+ not_found_val)` — Interpret the value as a list of items separated by separator then compare string against each value in the list. [#str-in-list]


## List manipulation

**list_count —** *2 arguments* — `list_count(value, separator)` — Interprets the value as a list of items separated by separator and returns the number of items in the list. [#list-count]

**list_count_field —** *1 argument* — `list_count_field(lookup_name)` — Returns the count of items in the field with the lookup name lookup_name. [#list-count-field]

**list_count_matching —** *3 arguments* — `list_count_matching(value, pattern, separator)` — Interprets value list items separated separator, returning number items list that match regular expression pattern. [#list-count-matching]

**list_difference —** *3 arguments* — `list_difference(list1, list2, separator)` — Return a list made by removing from list1 any item found in list2 using a case-insensitive comparison. [#list-difference]

**list_equals —** *6 arguments* — `list_equals(list1, sep1, list2, sep2, yes_val, no_val)` — Return yes_val if list1 and list2 contain the same items, otherwise return no_val. [#list-equals]

**list_intersection —** *3 arguments* — `list_intersection(list1, list2, separator)` — Return a list made by removing from list1 any item not found in list2 using a case-insensitive comparison. [#list-intersection]

**list_join —** *3 arguments* — `list_join(with_separator, list1, separator1 [, list2, separator2\*])` — Return list made joining items source lists using with_separator between items result list. [#list-join]

**list_re —** *4 arguments* — `list_re(src_list, separator, include_re, opt_replace)` — Construct a list by first separating src_list into items using the separator character. [#list-re]

**list_re_group —** *4 arguments* — `list_re_group(src_list, separator, include_re, search_re [,template_for_group\*])` — Like list_re except replacements are not optional. [#list-re-group]

**list_remove_duplicates —** *2 arguments* — `list_remove_duplicates(list, separator)` — Return a list made by removing duplicate items in list. [#list-remove-duplicates]

**list_sort —** *3 arguments* — `list_sort(value, direction, separator)` — Return value sorted using a case-insensitive lexical sort. [#list-sort]

**list_split —** *3 arguments* — `list_split(list_val, sep, id_prefix)` — Splits list_val into separate values using sep, then assigns the values to local variables named id_prefix_N where position value list. [#list-split]

**list_union —** *3 arguments* — `list_union(list1, list2, separator)` — Return a list made by merging the items in list1 and list2, removing duplicate items using a c7ase-insensitive comparison. [#list-union]

**range —** *1–4 arguments* — `range([start, ]stop[, step][, limit])` — Returns list numbers generated looping over range specified parameters start, stop, step, with maximum length limit. [#range]

**subitems —** *3 arguments* — `subitems(value, start_index, end_index)` — This function breaks apart lists of tag-like hierarchical items such as genres. [#subitems]

**sublist —** *4 arguments* — `sublist(value, start_index, end_index, separator)` — Interpret value list items separated separator, returning list made from items from start_index end_index. [#sublist]


## Other

**arguments —** *1–infinite arguments* — `arguments(id[=expression] [, id[=expression]\*])` — Used in a stored template to retrieve the arguments passed in the call. [#arguments]

**assign —** *2 arguments* — `assign(id, value)` — Assigns value to id, then returns value. [#assign]

**globals —** *1–infinite arguments* — `globals(id[=expression] [, id[=expression]\*])` — Retrieves “global variables” that can be passed into the formatter. [#globals]

**is_dark_mode —** *0 arguments* — `is_dark_mode()` — Returns '1' if calibre is running in dark mode, ''  otherwise. [#is-dark-mode]

**print —** *1–infinite arguments* — `print(a [, b\*])` — Prints the arguments to standard output. [#print]

**set_globals —** *1–infinite arguments* — `set_globals(id[=expression] [, id[=expression]\*])` — Sets globalvariables that can be passed into the formatter. [#set-globals]


## Recursion

**eval —** *1 argument* — `eval(string)` — Evaluates the string as a program, passing the local variables. [#eval]

**template —** *1 argument* — `template(x)` — Evaluates x as a template. [#template]


## Relational

**cmp —** *5 arguments* — `cmp(value, y, lt, eq, gt)` — Compares value and y after converting both to numbers. [#cmp]

**first_matching_cmp —** *2 arguments* — `first_matching_cmp(val, [ cmp, result, ]\* else_result)` — Compares val < cmp in sequence, returning the associated result for the first comparison that succeeds. [#first-matching-cmp]

**strcmp —** *5 arguments* — `strcmp(x, y, lt, eq, gt)` — Does a case-insensitive lexical comparison of x and y. [#strcmp]

**strcmpcase —** *5 arguments* — `strcmpcase(x, y, lt, eq, gt)` — Does a case-sensitive lexical comparison of x and y. [#strcmpcase]


## String manipulation

**character —** *1 argument* — `character(character_name)` — Returns the character named by character_name. [#character]

**check_yes_no —** *4 arguments* — `check_yes_no(field_name, is_undefined, is_false, is_true)` — Checks if value yes/no field named lookup name field_name values specified parameters, returning 'Yes' if match found otherwise returning empty [#check-yes-no]

**contains —** *4 arguments* — `contains(value, pattern, text_if_match, text_if_not_match)` — Checks if the value is matched by the regular expression pattern. [#contains]

**field_exists —** *1 argument* — `field_exists(lookup_name)` — Checks if a field  with the lookup name lookup_name exists, returning '1' if so and the empty string if not. [#field-exists]

**ifempty —** *2 arguments* — `ifempty(value, text_if_empty)` — If the value is not empty then return that value, otherwise return text_if_empty. [#ifempty]

**re —** *3 arguments* — `re(value, pattern, replacement)` — Return the value after applying the regular expression. [#re]

**re_group —** *2 arguments* — `re_group(value, pattern [, template_for_group\*])` — Return string made applying regular expression pattern value replacing each matched instance with value returned corresponding template. [#re-group]

**shorten —** *4 arguments* — `shorten(value, left_chars, middle_text, right_chars)` — Return shortened version value, consisting left_chars characters from beginning value, followed middle_text, followed right_chars characters from value. [#shorten]

**strcat —** *1–infinite arguments* — `strcat(a [, b\*])` — Returns a string formed by concatenating all the arguments. [#strcat]

**strcat_max —** *2–infinite arguments* — `strcat_max(max, string1 [, prefix2, string2\*])` — Returns a string formed by concatenating the arguments. [#strcat-max]

**strlen —** *1 argument* — `strlen(value)` — Returns the length of the string value. [#strlen]

**substr —** *3 arguments* — `substr(value, start, end)` — Returns the start’th through the end’th characters of value. [#substr]

**swap_around_articles —** *2 arguments* — `swap_around_articles(value, separator)` — Returns the value with articles moved to the end, separated by a semicolon. [#swap-around-articles]

**swap_around_comma —** *1 argument* — `swap_around_comma(value)` — Given a value of the form B, A, return A B. [#swap-around-comma]

**test —** *3 arguments* — `test(value, text_if_not_empty, text_if_empty)` — Return text_if_not_empty if the value is not empty, otherwise return text_if_empty. [#test]

**transliterate —** *1 argument* — `transliterate(value)` — Return a string in a latin alphabet formed by approximating the sound of the words in value. [#transliterate]


## URL functions

**encode_for_url —** *2 arguments* — `encode_for_url(value, use_plus)` — Returns the value encoded for use in a URL as specified by use_plus. [#encode-for-url]

**make_url —** *3–infinite arguments* — `make_url(path, [query_name, query_value]+)` — This function is the easiest way to construct a query URL. [#make-url]

**make_url_extended —** *1 argument* — `make_url_extended(...)` — This function is similar to make_url but gives you more control over the URL components. [#make-url-extended]

**query_string —** *3–infinite arguments* — `query_string([query_name, query_value, how_to_encode]+)` — Returns a URL query string constructed from the query_name, query_value, how_to_encode triads. [#query-string]

**to_hex —** *1 argument* — `to_hex(val)` — Returns the string val encoded into hex. [#to-hex]

**urls_from_identifiers —** *2 arguments* — `urls_from_identifiers(identifiers, sort_results)` — Given comma-separated list identifiers, where identifier colon-separated pair values returns comma-separated list HTML URLs generated from identifiers. [#urls-from-identifiers]

