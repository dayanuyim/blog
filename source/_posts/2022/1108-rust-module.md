---
title: RUST Module
date: 2022-11-08 23:25:19
categories: proglang
tags: rust
---

### scope

```rust
pub mod bar {
    fn foo(){}
    pub fn foo(){}
    pub(crate) fn foo(){}
    pub(super) fn foo(){}
    pub(in crate::mod1::mod2) fn foo(){}
}
```

- no modifier
    private scope, only visible within the same module or child modules.
- pub
    public scope
- pub(super)
    only visible to the parent module.
- pub(crate)
    only visible within the crate defined.
- pub(in crate::mod1::mod2)
    only visible to the specifed module and descendant modules.

### module

```rust
mod bar;
mod bar::foo;
```
could be `bar.rs` and `bar/foo.rs`; or `bar/mod.rs` and `bar/foo.rs`

### library

- `src/lib.rs` forms the root module of the library.
- put main to `src/bin/`


### attributes

- `#[attr]` for a single function or method, `#![attr]` for a single document, usually put in the top of the doc.
