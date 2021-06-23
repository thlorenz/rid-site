+++
title = "rid::store"
description = "Explains the Rid store attribute."
date = 2021-05-13
updated = 2021-05-13
draft = false
weight = 21 
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "Explains the Rid store attribute."
toc = true
top = false
+++


## Store 

There is exactly one _main_ model per application which is called the `Store`. It is annotated
with `#[rid::store].` The _Store_ is the _main_ entry point to the application's state.

A _Store_ is a `#[rid::model]` with some special characteristics. You can think of a
`#[rid:store]` as a superset of a `#[rid::model]`.

```rust
#[rid::store]
#[rid::structs(Todo)]
#[rid::enums(Filter)]
pub struct Store {
    last_added_id: u32,
    todos: Vec<Todo>,
    filter: Filter,
}
```

The `#[rid::store]` _struct_ type has to be called `Store`. This is a convention that enables
certain _rid_ features and makes it easier to use and configure.

### Annotations about Non-Primitive Types

As you can see we include type information of `Todo` and `Filter` via `rid::structs` and
`rid::enums` respectively. This is necessary because the `rid::store` attribute only has access
to the annotated `Store` struct and thus no way to deduce this information.

However in order to generate correct wrappers to access the _Model_ fields it needs this
information.

👉 read more about [structs and enums](./../structs-enums/).


The same is true for when creating _Models_ with the `#[rid::model]` attribute.

## The `rid:RidStore` Trait

The _Store_ has to implement the `RidStore` trait which looks as follows:

```rust
pub trait RidStore<TMsg> {
    fn create() -> Self;
    fn update(&mut self, req_id: u64, msg: TMsg);
}
```

The `create` method initializes the _Store_ and is only called once.

The `update` method is called each time a _message_ of type `TMsg` is received. Note how it is
called with the `req_id` which needs to be included with the message _reply_ via `rid::post` in
order to associate the two.

### Example `rid:RidStore` Trait Implementation

```rust
impl rid::RidStore<Msg> for Store {
     fn create() -> Self {
        let todos = vec![Todo { title: "Learn Rust".to_string() }]; 
        Self { todos }
    }
    
    fn update(&mut self, req_id: u64, msg: Msg) {
        match msg {
            Msg::AddTodo(title) => {
                self.todos.push(Todo { title });
                rid::post(Reply::AddedTodo(req_id));
            }
        }
    }
}

#[rid::message(Reply)]
pub enum Msg { AddTodo(String) }

#[rid::reply]
pub enum Reply { AddedTodo(u64) }
```

Here we reply to the `Add` _message_ with the `Added` _reply_ including the `req_id`. 

This allows rid to wait for the _message_ to be handled by waiting for a _reply_ whose `req_id`
matches that of the sent _message_. As a result user code can `await` a _message_ being
handled on the Dart side.

```dart
await Store.instance.msgAddTodo("Understand Messages and Replies");
```

👉 read more about [message and reply](./../message-reply/)
