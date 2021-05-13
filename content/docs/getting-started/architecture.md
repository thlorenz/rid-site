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

The UI, implemented in Flutter/Dart, concerns itself only with rendering _Widgets_ and user
interaction. It delegates to Rust for all application logic.

The application state is held by Rust. Application logic mutating that state is implemented in
Rust.

## Rust State and Logic Implementation

Your application will typically have one main model which holds the application state. This
state is not modified from the UI. Instead the UI sends messages to Rust in order to relay user
interaction. Rust then modifies the state of the application.

All logic needed to derive the new state from the previous one as a result of a user
interaction is implemented in Rust. Flutter only consumes this state and when needed
transforms it slightly and only locally to make it presentable via a _Widget_.

Flutter should never modify the global application state directly.

### Model and Message

The main model _struct_ of your application holds all its state. It can reference other model
_structs_ however it should be the only one receiving messages.

The message type is defined via an enum. It is used to send messages to the model and should be
the only means of mutating it.

**Example**

```rust
#[rid::model]
pub struct Model {
    count: u32,
}

impl Model {
    fn update(&mut self, msg: Msg) {
        match msg {
            Msg::Inc => self.count += 1,
            Msg::Add(n) => self.count += n,
        }
    }
}

#[rid::message(Model)]
pub enum Msg {
    Inc,
    Add(u32),
}

```

As you can see the `#[rid::message(Model)]` attribute defines the `Model` as the `Msg`
receiver. That model type needs to implement an update method `fn update(&mut self, msg: Msg)`
in order to handle incoming messages. 

_Note_: we omitted the `rid::export` method to initialize the model from Flutter. This is
explained in [rid::model](../../rid-attributes/model/)

_See also_: [rid::message](../../rid-attributes/message/)

## Accessing State and Sending Messages 

As mentioned, all state is held by Rust. It is exposed to Flutter via a _Getter_ based API. In
order to improve performance state is only transfered once accessed, i.e. an item of a
`Vec<u8>` are only passed once it is indexed into.

Flutter sends messages in response to user interaction like a button click.

In order to interact with the above Rust model from Flutter we could do the following. 

```dart, hl_lines=2 12 20 24
class _MyHomePageState extends State<MyHomePage> {
  final model = rid_ffi.initModel();

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('You have counted to:'),
            Text('${model.count}'),
          ],
        ),
      ),
      floatingActionButton: Row(
        mainAxisAlignment: MainAxisAlignment.end,
        children: [
          FloatingActionButton(
            onPressed: () => setState(() { model.msgAdd(10); }),
            child: Icon(Icons.add),
          ),
          FloatingActionButton(
            onPressed: () => setState(() { model.msgInc(); }),
            child: Icon(Icons.add),
          ),
        ],
      ),
    );
  }
}
```

## How Data is Passed

- primitives like `u8`, `i32` and C-style `enum`s are copied and passed _by value_
- strings like `String`, `&str` are passed _by reference_, but Rid immediately releases them after
  converting into a Dart String
- structs are passed as pointers _by reference_ and expose _Getters_ to access their fields
- collections like `Vec` are passed _by reference_ and Rid exposes an _Iterable_ interface to
  provide access to each item

### Iteratable Interface for Collections

_Rid_ provides access to the _pointer_ of a collection via a convenient _iterable_ interface to
the developer.

Have a look at the below list of `todos` defined on the `model`.

```rust
#[rid::model]
#[Debug, rid::Debug]
pub struct Todo {
    title: String
}

#[rid::model]
#[rid::structs(Todo)]
pub struct Model {
    todos: Vec<Todo>,
}
```

_Note_: that we derive `#[Debug, rid::Debug]` for the `Todo` to call it from Flutter via
`todo.debug([pretty])`, see [rid::debug](../../rid-attributes/debug/).

Those `todos` can be used on the Flutter end like any _Iterable_.

```dart
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
