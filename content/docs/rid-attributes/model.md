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

This `Todo` is exposed to Dart via a _pointer_. However its fields are accessible as if it was
a Dart class.

```dart
final Pointer<Todo> todo = todos[0];
final status = todo.completed ? 'done' : 'pending';
print('${todo.title} with id ${todo.id} is $status');
```

## Main Model

There is usually one _main_ model per application which is the _main_ entry point to the
application's state. The _struct_ is annotated like any other model.

```rust
#[rid::model]
#[rid::structs(Todo)]
#[rid::enums(Filter)]
pub struct Model {
    last_added_id: u32,
    todos: Vec<Todo>,
    filter: Filter,
}
```

_Note_ that we have to include type information of `Todo` and `Filter` via `rid::structs` and
`rid::enums` respectively. This is necessary because the `rid::model` attribute only has access
to the annotated `Model` struct and thus no way to deduce this information.  _See_ [structs and
enums](./../structs-enums/).

### Main Model `init` method

TODO explain

```rust
#[rid::export]
impl Model {
    #[rid::export(initModel)]
    fn new() -> Self {
        Self {
            last_added_id: 0,
            todos: vec![],
            filter: Filter::All,
        }
    }
  }
```

### Main Model `update` method

In order to receive _messages_ and update its state accordingly, the main model implements an
update method matching the message type.

```rust
#[rid::message(Model)]
pub enum Msg {
    AddTodo(String),
    RemoveTodo(u32),

    ToggleTodo(u32),

    SetFilter(Filter),
}

impl Model {
    fn update(&mut self, msg: Msg) { 
        use Msg::*;
        match msg {
            AddTodo(title) => {
                self.last_added_id += 1;
                let todo = Todo {
                    id: self.last_added_id,
                    title,
                    completed: false,
                };
                self.todos.push(todo);
            }
            
            RemoveTodo(id) =>  [ .. ],

            ToggleTodo(id) => [ .. ],
            
            SetFilter(filter) => self.filter = filter,
        };
    }
}
```
