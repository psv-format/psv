# psv

- [Github Page Version](psv-format.github.io)
- [Github Repo (If you want to contribute)](https://github.com/psv-format/psv-format.github.io)
- [Current Draft Spec](./spec.md)

PSV spec, with reference implementations in C and JavaScript

PSV is a restricted version of common pipe table syntax seen in Markdown extentions, with a spec and BSD-licensed reference implementations in C and JavaScript.

This repository contains the spec itself, along with tools for running tests against the spec, and for creating HTML and PDF versions of the spec.

Initial Start Of This Concept: https://talk.commonmark.org/t/side-thoughts-promoting-pipe-tables-as-a-potential-alternative-to-csv-format-e-g-psv/1862

## File Extention

`*.psv`

## Objective

The objective of this specification is to have a file extention like `.psv` that can be reliably converted from one data format to another. For example you may want to convert the below table to a json format.

```
# Table Project

## Sheet 1

| Tables   | Are           | Cool  |  
|----------|---------------|-------| 
| col 1 is | ok            | $1600 | 
| col 2 is | cool          | $12   | 
| col 3 is | normal        | $1    | 
```

It is expected that humans will be mostly editing the data. While machines will be consuming and producing these .psv files most of the time.

The spec will define a canonical recommended output, while being permissive in it's input to account for human editing.

It is not ment to replace csv or json. But rather straddle the middle ground of complexity. Where at least a clearly defined table header name is required and that it be viewable and editable by both humans and machine.

If possible it is also aimed to be renderable by commonmark as a document after Commonmark supports pipe tables.

**This is the initial objective. If you have a suggestion to change/improve the direction of this then feel free to suggest in the orignal [commonmark post](https://talk.commonmark.org/t/side-thoughts-promoting-pipe-tables-as-a-potential-alternative-to-csv-format-e-g-psv/1862)**

### Difference from common pipe table syntax

* No table alignment supported - This is a data format, not a display format
* Focus on minimal complexity - This is to make it easier to test implementation and encourage adoption.

## Status

* Status as of 2018-1-25
   - Start of the project.
