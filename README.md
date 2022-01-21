yeslogic-ucd-generate
=====================

A command line tool to generate Unicode tables in Rust source code. Tables
can typically be generated in one of three formats: a sorted sequence of
character ranges, a
[finite state transducer](https://github.com/BurntSushi/fst)
or a compressed trie. Full support for name canonicalization is also provided.
This tool also supports serializing regular expressions as DFAs using the
[regex-automata](https://github.com/BurntSushi/regex-automata)
crate.

<div align="center">
  <a href="https://app.travis-ci.com/github/yeslogic/ucd-generate">
    <img src="https://app.travis-ci.com/yeslogic/ucd-generate.svg?branch=master" alt="Build Status"></a>
  <a href="https://crates.io/crates/yeslogic-ucd-generate">
    <img src="https://img.shields.io/crates/v/yeslogic-ucd-generate.svg" alt="Version">
  </a>
</div>

<br>

This version of `ucd-generate` adds the following on top of BurntSushi's
version:

* `joining-group` sub-command.
  [#32](https://github.com/BurntSushi/ucd-generate/pull/32)
* `canonical-combining-class` sub-command.
  [#45](https://github.com/BurntSushi/ucd-generate/pull/45)

### Installation

Since this is mostly intended as a developer tool for use while writing Rust
programs, the principle method of installation is from crates.io:

```
$ cargo install yeslogic-ucd-generate
yeslogic-ucd-generate --help
```


### Example

This somewhat arbitrary example shows the output of generating tables for
three properties, and representing them as normal Rust character literal
ranges.

To run the example, you need to download the Unicode Character Database (UCD):

```
$ mkdir /tmp/ucd-14.0.0
$ cd /tmp/ucd-14.0.0
$ curl -LO https://www.unicode.org/Public/zipped/14.0.0/UCD.zip
$ unzip UCD.zip
```

Note that prior to version 13.0.0, `emoji/emoji-data.txt` file was distributed
separate from the UCD bundle. For these versions, you may need to download this
file from https://unicode.org/Public/emoji in order to generate certain tables.

Now tell `ucd-generate` what you want and point it to the directory created
above:

```
$ ucd-generate property-bool /tmp/ucd-14.0.0 --include Hyphen,Dash,Quotation_Mark --chars
```

And the output, which is valid Rust source code:

```rust
// DO NOT EDIT THIS FILE. IT WAS AUTOMATICALLY GENERATED BY:
//
//   ucd-generate property-bool /tmp/ucd-14.0.0 --include Hyphen,Dash,Quotation_Mark --chars
//
// Unicode version: 14.0.0.
//
// ucd-generate 0.2.10 is available on crates.io.

pub const BY_NAME: &'static [(&'static str, &'static [(char, char)])] = &[
  ("Dash", DASH), ("Hyphen", HYPHEN), ("Quotation_Mark", QUOTATION_MARK),
];

pub const DASH: &'static [(char, char)] = &[
  ('-', '-'), ('֊', '֊'), ('־', '־'), ('᐀', '᐀'), ('᠆', '᠆'),
  ('‐', '―'), ('⁓', '⁓'), ('⁻', '⁻'), ('₋', '₋'),
  ('−', '−'), ('⸗', '⸗'), ('⸚', '⸚'), ('⸺', '⸻'),
  ('⹀', '⹀'), ('\u{2e5d}', '\u{2e5d}'), ('〜', '〜'), ('〰', '〰'),
  ('゠', '゠'), ('︱', '︲'), ('﹘', '﹘'), ('﹣', '﹣'),
  ('－', '－'), ('𐺭', '𐺭'),
];

pub const HYPHEN: &'static [(char, char)] = &[
  ('-', '-'), ('\u{ad}', '\u{ad}'), ('֊', '֊'), ('᠆', '᠆'),
  ('‐', '‑'), ('⸗', '⸗'), ('・', '・'), ('﹣', '﹣'),
  ('－', '－'), ('･', '･'),
];

pub const QUOTATION_MARK: &'static [(char, char)] = &[
  ('"', '"'), ('\'', '\''), ('«', '«'), ('»', '»'), ('‘', '‟'),
  ('‹', '›'), ('⹂', '⹂'), ('「', '』'), ('〝', '〟'),
  ('﹁', '﹄'), ('＂', '＂'), ('＇', '＇'), ('｢', '｣'),
];
```


### Contributing

The `ucd-generate` tool doesn't have any specific design goals, other than to
collect Unicode table generation tasks. If you need `ucd-generate` to do
something and it's reasonably straight-forward to add, then just submitting a
PR would be great. Otherwise, file an issue and we can discuss.


### Future work

This tool is by no means is exhaustive. In fact, it's not even close to
exhaustive, and it may never be. For the most part, the intent of this tool
is to collect virtually any kind of Unicode generation task. In theory, this
would ideally replace the hodge podge collection of Python programs that is
responsible for this task today in various Unicode crates.

It is likely, and perhaps desirable, that this tool will eventually be
deprecated in favor of a more complete project like
[UNIC](https://github.com/behnam/rust-unic).
The `ucd-generate` tool was born out of desire to add more principled Unicode
support to Rust's regex crate, and it was much easier to develop this
out-of-band for my specific requirements.

Finally, the structures generated by this tool may not be optimal. In
particular, I strongly suspect that the trie set generator could be improved
dramatically.


### Sub-crates

This repository is home to three sub-crates:

* [`ucd-parse`](ucd-parse) - A crate for parsing UCD files into
  structured data.
* [`ucd-trie`](ucd-trie) - Auxiliary type for handling the trie
  set table format emitted by `ucd-generate`. This crate has a `no_std` mode.
* [`ucd-util`](ucd-util) - A purposely small crate for Unicode
  auxiliary functions. This includes things like symbol or character name
  canonicalization, ideograph name generation and helper functions for
  searching property name and value tables.


### License

This project is licensed under either of
 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or
   https://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or
   https://opensource.org/licenses/MIT)
at your option.
