+++
title = "Application Architecture"
description = "Explains the Rid application architecture."
date = 2021-05-13
updated = 2021-05-13
draft = false
weight = 20
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "Explains the Rid application architecture."
toc = true
top = false
+++

## UI separated from App Logic

Each application created with Rid is divided into two major parts. 

The UI implemented in Flutter/Dart concerns itself only with rendering _Widgets_ and user
interaction. It delegates to Rust for all application logic.

All application state is held by Rust. Application logic mutating that state is implemented in
Rust.

## Rust State and Logic Implementation

A _rid_ application has one main model, the _Store_, which holds the application state. This
state is not modified from the UI. Instead the UI sends messages to Rust in order to relay user
interaction. Rust then modifies the state of the application and responds with a _Reply_ in
order to communicate that the message has been handled. At that point the UI can query the
state of the _Store_ and update itself.

All logic needed to derive the new state from the previous one as a result of a user
interaction is implemented in Rust. Flutter only consumes this state and when needed
transforms it slightly and only locally in order to make it presentable via a _Widget_.

Flutter should never modify the global application state directly.

### Store, Message and Reply

The _Store_ [_struct_](https://doc.rust-lang.org/std/keyword.struct.html) of your application
holds all the application's state. The _Store_ can reference other model _structs_.

Rid assumes that all _Messages_ may be handled asynchronously and thus will never make the
assumption that the state of the _Store_ was modified in response to it after the `update`
method completes.

Instead it uses a _Request/Reply_ mechanism to allow signaling that the state of the _Store_
was completely updated in response to a _Message_.

The _Message_ is defined via an [_enum_](https://doc.rust-lang.org/std/keyword.enum.html). It is used to send messages to the model and should be
the only means of mutating it. The variants of the message _enum_ can have associated data
which is used to pass a message payload from Dart (see `Msg::Add(u32)` below).

The _Message_ enum is associated with a _Reply_ enum which is used to respond to messages after
they are handled.

**Example**

```rust
#[rid::store]
pub struct Store {
    count: u32,
}

impl rid::RidStore<Msg> for Store {
    fn create() -> Self {
        Self { count: 0 }
    }

    fn update(&mut self, req_id: u64, msg: Msg) {
        match msg {
            Msg::Inc => {
                self.count += 1;
                rid::post(Reply::Increased(req_id));
            }
            Msg::Add(n) => {
                self.count += n;
                rid::post(Reply::Added(req_id, n.to_string()));
            }
        }
    }
}

#[rid::message(Reply)]
#[derive(Debug, Clone)]
pub enum Msg {
    Inc,
    Add(u32),
}

#[rid::reply]
#[derive(Clone)]
pub enum Reply {
    Increased(u64),
    Added(u64, String),
}
```

As you can see the `#[rid::message(Reply)]` attribute defines the type of the _Reply_ used to
respond to _Messages_.

👉 read more about [rid::message and rid::reply](../../rid-attributes/message-reply/)

## Accessing State and Sending Messages 

As mentioned, all state is held by Rust. It is exposed to Flutter via a _Getter_ based API.
State is only transfered once accessed in order to improve performance. To make things easier
the recommended API converts all data to Dart instances to avoid memory races and access
issues. 

TODO: link separate document of higher level API details

For cases where more control is required and the performance suffers, i.e. when sending huge
lists of items, a lower level API is provided as well. As an example when using that API an item of a
`Vec<u8>` is only passed once it is accessed by indexing into the _collection_. However the
developer is now responsible to properly _lock_ the _Store_ to ensure that this vector wasn't
mutated in the meantime.

TODO: link separate document of raw API details

In response to user interaction like a button click we send messages to Rust in order to cause
the _Store_ to be updated.

Using the recommended higher level API to interact with the above Rust _Store_ from Flutter we
could do the following. 

```dart, hl_lines=2 12 21 27
class _MyHomePageState extends State<MyHomePage> {
  final store = Store.instance;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('You have counted to:'),
            Text('${store.count}'),
          ],
        ),
      ),
      floatingActionButton: Row(
        mainAxisAlignment: MainAxisAlignment.end,
        children: [
          FloatingActionButton(
            onPressed: () => { 
              store.msgAdd(10).then((_) => setState(() {})); 
            }),
            child: Icon(Icons.add),
          ),
          FloatingActionButton(
            onPressed: () => { 
              store.msgInc(10).then((_) => setState(() {})); 
            }),
            child: Icon(Icons.add),
          ),
        ],
      ),
    );
  }
}
```

## Rid's Raw Api

TODO: This is a quick summary of the _raw_ rid API. It will be moved into its own doc shortly and a
doc for the _recommended_ higher level rid API will be provided alongside it.

### How Data is Passed

- primitives like `u8`, `i32` and C-style `enum`s are copied and passed _by value_
- strings like `String`, `&str` are passed _by reference_, but Rid immediately releases them after
  converting into a Dart String
- structs are passed as pointers _by reference_ and expose _Getters_ to access their fields
- collections like `Vec` are passed _by reference_ and Rid exposes an _Iterable_ interface to
  provide access to each item

### Accessing and Iterating Collections

_Rid_ wraps the retrieved _pointer_ of a _collection_ in an API that exposes a convenient
_iterable_ interface as well as an indexing operator.

Have a look at the below list of `todos` defined on the `model`.

```rust
#[rid::model]
#[derive(Debug, rid::Debug)]
pub struct Todo {
    title: String
}

#[rid::model]
#[rid::structs(Todo)]
pub struct Model {
    todos: Vec<Todo>,
}
```

_Note_: that we `#[derive(Debug, rid::Debug)]` for the `Todo` in order to call it from Flutter
via `todo.debug([pretty])`, see [rid::debug](../../rid-attributes/debug/).

Those `todos` can be used on the Flutter end like any _Iterable_.

```dart, hl_lines = 3 7 11 16
queryTodos(Pointer<Model> model) {
  final todos = model.todos;
  final total = todos.length;

  print("Total Todos:     $total");
  print("\nTodos:");
  for (final todo in matchingTodos.iter()) {
    print("    ${todo.debug()}");
  }
  
  final firstTodo = todos[0];
  final todoTitles = todos.iter().map((todo) => todo.title);
  final todoSummedIds =
      todos.iter().map((todo) => todo.id).reduce((acc, id) => acc + id);
  final todosUrgent =
      todos.iter().where((todo) => todo.title.contains('urgent'));
}
```

_See also_: [#rid::structs](../../rid-attributes/structs-enums/)
