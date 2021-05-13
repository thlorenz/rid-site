+++
title = "Introduction"
description = "AdiDoks is a Zola theme helping you build modern documentation websites, which is a port of the Hugo theme Doks for Zola."
date = 2021-05-01T08:00:00+00:00
updated = 2021-05-01T08:00:00+00:00
draft = false
weight = 10
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "AdiDoks is a Zola theme helping you build modern documentation websites, which is a port of the Hugo theme <a href=\"https://github.com/h-enk/doks\">Doks</a> for Zola."
toc = true
top = false
+++

## Quick Start

One page summary of how to start a new AdiDoks project. [Quick Start →](../quick-start/)

```rust
#[rid::model]                 // this is a model and all its fields should be accessible from Dart
#[rid::structs(Todo)]         // the referenced Todo type is a struct
#[rid::enums(Filter)]         // the referenced Filter type is an enum
#[derive(Debug, rid::Debug)]  // expose a `model.debug(pretty?)` function to Dart 
pub struct Model {
    last_added_id: u32,
    todos: Vec<Todo>,
    filter: Filter,
}
```

## Go further

Contributing and Help.

## Contributing

Find out how to contribute to Doks. [Contributing →](../../contributing/how-to-contribute/)

## Help

Get help on Doks. [Help →](../../help/faq/)
