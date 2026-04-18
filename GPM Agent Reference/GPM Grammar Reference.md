# General Program Mode (GPM) — Grammar Reference

A compact, **source-of-truth** quick reference for Calibre’s template language in **General Program Mode** (GPM). You write a GPM template as a single program starting with `program:`.

---

## Invocation

```
program: <expression_list>
```

> The value of the **template** is the value of the last evaluated top-level expression (or what you `return`).

---

## Grammar (verbatim)

```
program         ::= 'program:' expression_list
expression_list ::= top_expression [ ';' top_expression ]*
top_expression  ::= or_expression
or_expression   ::= and_expression [ '||' and_expression ]*
and_expression  ::= not_expression [ '&&' not_expression ]*
not_expression  ::= [ '!' not_expression ]* | concatenate_expr
concatenate_expr::= compare_expr [ '&' compare_expr ]*
compare_expr    ::= add_sub_expr [ compare_op add_sub_expr ]
compare_op      ::= '==' | '!=' | '>=' | '>' | '<=' | '<' |
                    'in' | 'inlist' | 'inlist_field' |
                    '==#' | '!=#' | '>=#' | '>#' | '<=#' | '<#'
add_sub_expr    ::= times_div_expr [ add_sub_op times_div_expr ]*
add_sub_op      ::= '+' | '-'
times_div_expr  ::= unary_op_expr [ times_div_op unary_op_expr ]*
times_div_op    ::= '*' | '/'
unary_op_expr   ::= [ add_sub_op unary_op_expr ]* | expression
expression      ::= identifier | constant | function | assignment | field_reference |
                    if_expr | for_expr | break_expr | continue_expr | return_stmt |
                    '(' expression_list ')' | function_def
field_reference ::= '$' [ '$' ] [ '#' ] identifier
identifier      ::= id_start [ id_rest ]*
id_start        ::= letter | underscore
id_rest         ::= id_start | digit
constant        ::= " string " | ' string ' | number
function        ::= identifier '(' expression_list [ ',' expression_list ]* ')'
function_def    ::= 'def' identifier '(' top_expression [ ',' top_expression ]* ')' ':'
                    expression_list 'fed'
assignment      ::= identifier '=' top_expression
if_expr         ::= 'if' condition 'then' expression_list
                    [ elif_expr ] [ 'else' expression_list ] 'fi'
condition       ::= top_expression
elif_expr       ::= 'elif' condition 'then' expression_list elif_expr | ''
for_expr        ::= for_list | for_range
for_list        ::= 'for' identifier 'in' list_expr
                    [ 'separator' separator_expr ] ':' expression_list 'rof'
for_range       ::= 'for' identifier 'in' range_expr ':' expression_list 'rof'
range_expr      ::= 'range' '(' [ start_expr ',' ] stop_expr
                    [ ',' step_expr [ ',' limit_expr ] ] ')'
list_expr       ::= top_expression
break_expr      ::= 'break'
continue_expr   ::= 'continue'
return_stmt     ::= 'return' top_expression
separator_expr  ::= top_expression
start_expr      ::= top_expression
stop_expr       ::= top_expression
step_expr       ::= top_expression
limit_expr      ::= top_expression
```

---

## Values, truthiness & types

- Every **top_expression** has a value. The value of an **expression_list** is the value of its **last** top_expression.
- **Truthiness**: non-empty string/number ⇒ *True* (`'1'` when an operator needs a boolean); empty string `''` ⇒ *False*.
- Strings and numbers are interchangeable where sensible (e.g., `10` and `'10'`).
- Lines beginning with `#` (optionally preceded by spaces/tabs) are **comments**.

---

## Operator precedence (highest → lowest)

1. Function calls, constants, parenthesized expressions, statement expressions, assignments, field references
2. Unary plus `+` / minus `-` (right-to-left). If a calculation ends with a `.0` fractional part, it’s converted to an integer (e.g., `3.0` → `3`).
3. Multiply `*`, divide `/` (left-to-right)
4. Add `+`, subtract `-` (left-to-right)
5. Comparisons (return `'1'` or `''`; **not associative**, so `a < b < c` is a **syntax error**)
6. String concatenation `&` (left-to-right)
7. Logical NOT `!`
8. Logical AND `&&` (left-to-right, **short-circuits**)
9. Logical OR  `||` (left-to-right, **short-circuits**, inclusive)

---

## Field references

- Field references are purely lexical: the identifier after `$`, `$$`, or `$#` must be a **literal identifier** written directly in the program.
- Variables cannot supply the identifier for a field reference. For example, if `c = 'genre'`, then `$#c` is not interpreted as “the field named by the value of `c`”. It is interpreted literally as the field named `#c`. To reference a field whose name is stored in a variable, use `field(c)` or `raw_field(c)`.

- `$name`  ⇒ `field('name')`
- `$$name` ⇒ `raw_field('name')`
- Custom columns: `$#genre` ⇒ `field('#genre')`; `$$#my_int` ⇒ `raw_field('#my_int')`

---

## Control flow: `if`

```
if <condition> then
    <expression_list>
elif <condition> then
    <expression_list>
else
    <expression_list>
fi
```

- `condition` is **one** top_expression (no semicolons). The branches are semicolon-separated expression lists.
- `if` is an **expression**: it yields the last value computed in the executed branch (or `''` if no branch ran).

**Equivalents**

```
program: if field('series') then 'foo' else 'bar' fi
program: if field('series') then a='foo' else a='bar' fi; a
program: a = if field('series') then 'foo' else 'bar' fi; a
```

---

## Loops: `for`

### Over lists or fields

```
for i in <list_expr> [ separator <separator_expr> ] :
    <expression_list>
rof
```

- If `<list_expr>` evaluates to a **lookup name** (e.g., `tags`, `#genre`) the field is fetched and its configured separator is used.
- Otherwise it’s treated as a literal list (default separator `,` unless you provide `separator ...`).

### Over ranges

```
for i in range([start,] stop[, step[, limit]]):
    <expression_list>
rof
```

- `separator` **not allowed** with `range()`.
- Use `break` / `continue` inside loop bodies.

---

## Assignments & `return`

- Assignment: `name = <top_expression>` (its value is the assigned value).
- `return <expr>`: inside a function returns to caller; at top-level sets the template’s final value and exits immediately.

---

## Functions in GPM

- Call syntax: `fn(arg1, arg2, ...)` (arguments are **expression_lists**).
- **Unlike** Single Function Mode, in GPM you **must** provide the first `value` parameter yourself when a function expects it.
- Nested calls are allowed anywhere expressions are allowed.

### Local function definitions

```
def fname(p1[, p2[=default], ...]):
    <expression_list>
fed
```

- Arguments are **positional**. Defaults allowed (e.g., `a = 25`). Too many actual arguments is an error.
- Functions must be **defined before use**. You can use `return` inside them.

---

## Relational operators

**String comparisons** (case-insensitive lexical): `==`, `!=`, `<`, `<=`, `>`, `>=`, `in`, `inlist`, `inlist_field`

- `in` / `inlist` / `inlist_field`: the **left** operand is a **regular expression** tested against the right-hand value(s).
- `inlist`: right side is a comma-separated **string** list.
- `inlist_field`: right side evaluates to a **field name** and uses that field’s separator (**faster** than `inlist`).

**Numeric comparisons**: `==#`, `!=#`, `<#`, `<=#`, `>#`, `>=#`

- Empty string and the literal string `'None'` both evaluate to **0** for numeric comparisons.

All comparisons return `'1'` if true, else `''`.

