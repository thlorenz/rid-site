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
of that struct will be made available to Dart as _Getters_ on the model instance. Note that fields
cannot be mutated directly from the Dart side which is intentional, see [rid architecture](../../getting-started/architecture/).

## Model

```rust
#[rid::model]
pub struct Todo {
    id: u32,
    title: String,
    completed: bool,
}
```

`Todo` only has primitive types and is not the main entry point to the application's state.
Thus it is very simple to setup.

This `Todo` is represented on the Dart side via a Dart _class_ of the same name. This class has
overrides for _equality_ (`operator ==` and `hashCode`) as well as `toString` already
implemented for your convenience.

```dart
final Todo todo = Store.instance.todos[0];
final status = todo.completed ? 'done' : 'pending';
print('$todo');
```

A _raw_ pointer to the `Todo` is exposed to Dart as well via the lower level API. Using this
API is recommended only in cases in which you need more direct control for instance when you
need to tweak performance. It does not come with any of the memory safety guarantees that the
recommended API provides.

```dart
final Pointer<RawTodo> todo = Store.instance.raw.todos[0];
print('${todo.title} with id ${todo.id} is $status');
```
