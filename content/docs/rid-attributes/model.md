+++
title = "rid::model"
description = "Explains the Rid model attribute."
date = 2021-05-13
updated = 2021-05-13
draft = false
weight = 20
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "Explains the Rid model attribute."
toc = true
top = false
+++

## What is it?

Any Rust struct can be annotated with the `#[rid::model]` attribute. As a result all the fields
of that struct will be made available to Dart as _Getters_ on the instance. Note that fields
cannot directly mutated from the Dart side which is intentional, see [rid
architecture](./missing).

## Main Model

```rust
#[rid::model]
#[rid::structs(Todo)]
#[rid::enums(Filter)]
#[derive(Debug, rid::Debug)]
pub struct Model {
    last_added_id: u32,
    todos: Vec<Todo>,
    filter: Filter,
}
```

```rust
#[rid::model]
#[derive(Debug, rid::Debug, rid::Display)]
pub struct Todo {
    id: u32,
    title: String,
    completed: bool,
}
```

