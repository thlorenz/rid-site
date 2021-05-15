+++
title = "Introduction"
description = "Rid is a tool making it super easy to integrate application logic authored in Rust with Flutter UI code."
date = 2021-05-01T08:00:00+00:00
updated = 2021-05-01T08:00:00+00:00
draft = false
weight = 10
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "<a href=\"https://github.com/rid-examples\">Rid</a> makes it easy to integrate Rust application logic with your Flutter UI."
toc = true
top = false
+++

## Quick Start

Please have a look at the below snippets to get an idea of how _Rid_ helps you integrate
your Flutter/Dart UI with your application logic implemented in Rust. Then continue by reading
about the [Rid Appliation Architecture →](../architecture/)

**Rust**
```rust
pub struct Todo {
  title: String
}

#[rid::model]
#[rid::structs(Todo)]
pub struct Model {
    todos: Vec<Todo>,
}

#[rid::export]
impl Model {
    #[rid::export(initModel)]
    pub fn new() -> Self {
        let todos = vec![Todo { title: "Have fun with Rid".to_string() }]; 
        Self { todos }
    }
}
```

**Dart**
```dart 
final model = rid_ffi.initModel();
for (final todo in model.todos.iter()) {
    print("${todo.title}");
}
```

**Flutter**
```dart, hl_lines = 5 8 9 
class TodosView extends StatelessWidget {
  // [ .. ]
  @override
  Widget build(BuildContext context) {
    final todos = model.todos;
    return Center(
      child: ListView.builder(
      itemCount: todos.length,
      itemBuilder: (_, index) => Text('${todos[index].title}'),
    ));
  }
}
```

## Get Involved 

Please [sponsor _Rid_ development](https://github.com/sponsors/thlorenz) so it can evolve and stay maintained. 

## Contributing

Find out how to contribute to _Rid_. [Contributing →](../../contributing/how-to-contribute/)
