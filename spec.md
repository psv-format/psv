PSV Syntax
==========

The _Pipe-Separated Values_ (PSV) structured data file format is build with Markdown / Commonmark compatibility in mind. It is inspired by existing table extensions, e.g. from Github-Flavored Markdown (GFM), but makes some adjustments.

Inspirations
------------

- [GFM](https://github.github.com/gfm/#tables-extension- "GitHub Flavored Markdown")
- [MMD](http://fletcher.github.io/MultiMarkdown-5/tables.html "MultiMarkdown") / [PME](https://michelf.ca/projects/php-markdown/extra/#table "PHP Markdown Extra")
- [Marktable](https://github.com/nopnop/marktable)
- [Markdown-it MultiMD Table plugin](https://github.com/RedBug312/markdown-it-multimd-table)

File structure
--------------

Each PSV file consists of at least one _worksheet_. Each worksheet contains at least one _spreadsheet_. Each spreadsheet consists of at least one _table_. Conversely, each table belongs to exactly one spreadsheet and each spreadsheet belongs to exactly one worksheet. Each table consists of at least one _row_ and at least one _column_. Each intersection of a row and a column defines a _slot_. Each _cell_ spans at least one slot (and all of its slot must be spatially adjacent to each other, although they not necessarily form a rectangle).

Each worksheet and each spreadsheet starts with a _title_. They should be provided explicitly, but they can be anonymous. Tables are always anonymous. Worksheets and spreadsheets can contain other informative content in restricted Markdown format, interpreted with Commonmark rules applied.

Headings
--------

For conistency, heading syntax and row (group) dividers are aligned, i.e. they are using the same characters. Therefore generators must use underlined Setext style for top-level `====` _worksheet_ titles and second-level `----` _spreadsheet_ titles. Readers should also support `#` prefixed ATX style. Generators should use at least four characters for the underline and they should match the length of the heading text.

```````````````````````````````` markdown
Worksheet
=========

ignored content with **Markdown** formatting, should be informative to human readers or can be used for future extensions

Spreadsheet
-----------

_here be tables_
````````````````````````````````

Separators
----------

In _rows_, occuring in vertical progression after each other in a PSV file, the top-level marker, i.e. the equals sign `=`, consists of two parallel horizontal lines and the second-level marker, i.e. the hyphen-minus or _dash_ `-`, consists of a single horizontal line. Consequently, in _columns_, occuring in horizontal progression after each other (i.e. inside a line of text), the top-level marker should be double vertical lines and the second-level marker should be a single vertical line, i.e. the _pipe_ character `|`. The best character available in US ASCII for the double vertical line would be the broken bar `¦`, but since this is easily confused with the pipe and is not readily available on most keyboard layouts, the colon `:` is chosen instead.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ markdown
==========================
: Foo | Bar | Baz : Quuz :
--------------------------
| 1   | 2   | 3   | 1    |
==========================

:=================:======:
|-----|-----|-----|------|
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- The _double_ or _broken marks_, colon and equals, are used to delineate headers (and footers), the _single_ or _line marks_, pipe and dash, for normal cells.
- Horizontal tabulators augment or replace pipes, line breaks or empty lines augment or replace dashes.

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ markdown
| Column group header           |
|===============================|
| Column header | Column header |
|---------------|---------------|
|             1 |             2 |
|             3 |             4 |
|---------------|---------------|
| Column footer | Column footer |
|===============================|
| Column group footer           |


Row group : Row header | 1 | 2
header    : Row header | 3 | 4
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~



Contents, Tables
----------------

- Each header cell must contain a _label_ in its first slot and may contain a _data type hint_ or a _formatting hint_ for their column or row data in its last slot. 
- Footer cells should contain a _symbolic aggregation formula_. (These are similar to `SUBTOTAL()` in most spreadsheet applications.)
- **(Alpha:)** A _symbolic aggregation formula_ is a sequence of 3 math characters, e.g. `=^+`. The inital one can be repeated at the end, e.g. `=^+=`.
   1. `=` the start of a formula, alternatively `~` rounds, `<` rounds down, `>` rounds up
   2. `<` or `>` specify that the formula applies to the current row, `^` to the current column.
   3. operation:
      - `+` sum
      - `*` product
      - `#` count valid values according to the applicable data type hints
      - `?` count all truish (i.e. non-zero, non-false, non-empty) values 
      - `!` mode
      - `_` minimum, if string values: shortest
      - `^` maximum, if string values: longest
      - `<` 
      - `>` 
      - `%` median
      - `/` arithmetic mean, average
      - `$` geometric mean
      - `@` harmonic mean
      - `-` variance of a sample
      - `~` variance of a population
      - `:` standard deviation of a sample
      - `=` standard deviation of a population
      - `.` 
      - `,` 
      - `;` 
      - `'` 
      - `"`
      - `&` string concatenation

Structure
---------

- A _table_ consists of an optional _head_ in both directions, at least one mandatory _body_ and an optional _foot_ in both directions. Each _table_ consists of an optional _header_, followed by at least one mandatory _body_ and an optional _footer_. Every header, body and footer contain multiple rows, but no row must contain more cells than the table has columns.
- Simple cells cannot contain block content (paragraphs, lists, headings, quotations, fences). They can contain any Markdown inline formatting. Links should use collapsed or shortcut reference format.
- Cells can span multiple rows or columns or both.
- Outer lines are usually optional.

> 1. How do you make an optional field header? Is it assumed that the header is always the first cell plus separator? (That should be clear in the spec). Is that a bad idea? Since it requires holding the first line in memory until it can be confirmed if its a header or body.
> 2. I think for `*.psv`, we don’t really care if the cell content is markdown or not, as its just treated as a string. We could add `""` quotation around the value if it should be a string and not be autodetected as a number, boolean, or null. But is a good thing to note as recommendation for commonmark.
> 3. Cell spanning rows and columns is going to be an interesting problem to approach. Most pipe table implementation ignore it.

### Complex Cells (with block content)

Several possibilities

Markdown-it:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ markdown
| Column header  | Column header  |
| -------------- | -------------- |
| - complex cell | > complex cell \
| - complex cell | > complex cell \
| - complex cell | > complex cell |
| simple cell    | simple cell    |
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ markdown
| Column header  | Column header  |
| -------------- | -------------- |
| - complex cell | simple cell    |
| - complex cell | -------------- |
| - complex cell | -------------- |
| simple cell    | simple cell    |
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

### Column and Row Spans

Several possibilities

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ markdown
| Column header  | Column header  |
| -------------- | -------------- |
| column span    | <<<<<<<<<<<<<< |
| simple cell    | row span       |
| simple cell    | ^^^^^^^^^^^^^^ |
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MMD/PME:
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ markdown
| Column header  | Column header  |
| -------------- | -------------- |
| column span                    ||
| simple cell    | row span       |
| simple cell    : row span       :
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Addressing
----------

- Individual cells are addressable in links with case-sensitive standard spreadsheet A1 notation preceded by a hash sign `#`, e.g. `#A1`. IDs based upon column and row labels, e.g. `#[my_row]:[my_col]`, is not supported by default.
- (Internal) relative URLs work inside autolinks, e.g. `<#A1>`.
- Cell content can be embedded elsewhere with image link syntax, e.g. `![fallback](#A1)`.

Data types
----------

- String `"string"`, `'string'`
- Number `#0.#`
  - Integer `#0`
  - Float `#0,0#`
  - Date `0000-00-00`
  - Time `00:00:00.#`
  - Boolean `?`
  - with unit `0 %`
- Address `<#>`
- Array `[,]`
- Map `[:]`
- Set `{,}`
- Object `{:}`
