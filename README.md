# The Dead-Simple Object Notation (DSON) Data Interchange Format {§=off}

::: #toc

  {{ toc }}

:::

</div>

## Abstract

Dead-Simple Object Notation (DSON) is a data interchange format intended to be intuitive, forgiving and human-readable. This standard is an attempt to define the simplest possible interchange format yet enough to describe complex nested structures.

```dson
{
  'Is DSON simple?' = true, # Indeed
  rules = [
    "non-ASCII, control and whitespace
      characters must be quoted",
  ]
}
```

### General

Code point shortcuts used in the specification: {id=codepoints}

Code point | Shortcut
--- | ---
`%x20` (` `) | *Whitespace*
`%x0a` (`LF`),<br>`%x0d` (`CR`),<br>`%x0d.0a` (`CRLF`) | *Newline*
`%x2c` (`,`) | *Comma*

A *comment* begins with `%x23` (`#`), consists of zero (excluding `#`) or more code points and is terminated with a [newline](#codepoints). {id=comments}

[Whitespaces](#codepoints) and [newlines](#codepoints) are ignored outside of [quoted strings](#quoted-strings) and [comments](#comments).

A *value* is a string, an object or an array.

A DSON file may contain a single object or array.

### Strings

A *string* consists of zero or more code points and may be either quoted (§4.4) or unquoted (§4.3). A string consisting of zero code points is considered *empty*.

Any code point in a string may be *escaped* by prepending `%x5c` (`\`). An escaped code point is considered a part of the string. The `\` itself may be escaped as `\\`.

#### Unquoted strings

An *unquoted string* is a string consisting of code points in range `%x21`-`%x7e`, excluding the following code points unless escaped:

* `%x5b` (`[`);
* `%x5d` (`]`);
* `%x7b` (`{`);
* `%x7d` (`}`);
* `%x2c` (`,`).

An unquoted string is terminated with a comma.

An unquoted string is trimmed (i.e. whitespaces and newlines around it are removed). Therefore, an unquoted string consisting exclusively of whitespaces and newlines is considered empty.

#### Quoted strings

A *quoted string* is a string wrapped in a pair of unescaped quotes, i.e. `%x22` (`"`) or `%x27` (`'`).

#### Multiline strings

A *multiline string* is an unquoted string containing at least one newline.

A multiline string is aligned to the least-padded line, beginning from the second line.

A multiline string with the first line being empty has the first line ignored as if the string began from the second line.

A multiline string having a line ending with an escaped newline results in the following line appended to the previous one as if there was no newline.

### Objects

An *object* begins with `{` and is terminated with `}`.

An object may contain zero or more comma-separated key-value pairs called object properties.

An object property key is a string.

An object property key must be unique within an object.

An object property key and value are separated with a single `%x3d` (`=`) code point.

Trailing commas are allowed, but can be omitted.

### Arrays

An *array* begins with `[` and is terminated with `]`.

An array may contain zero or more comma-separated values.

Trailing commas are allowed, but can be omitted.

## Example

The following example is deliberately poorely formatted to demonstrate lenient-ness of the DSON format.

```dson
# The following DSON file contains # of settings
# for my program named "Foo"
{
  # Log settings
  log = {  # This is a nested DSON object!

     # Write into two streams (note
  # how the whitespace is escaped)
    path = ~/foo/bar.log\,\ $stdout,

    'level' =info, # 'warn' is the default level

    header= "
      A log file
     for my program\
   named \"Foo\"
    " # `···A·log·file\n··for·my·programnamed·"Foo"\n`
  },

  # File extensions to process
  extensions=[
    .jpg,
      '.💩', # Must be quoted, as it contains a Unicode point
] ,

    # A empty string
    exclude = ,

  # Also a empty string
  ignore =
}
```
