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
about the [Rid Application Architecture →](../architecture/)

**Rust**
```rust
#[rid::store]
#[rid::structs(Todo)]
pub struct Store {
    todos: Vec<Todo>,
}

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
pub enum Msg {
    AddTodo(String),
}

#[rid::reply]
pub enum Reply {
    AddedTodo(u64),
}

#[rid::model]
pub struct Todo {
  title: String
}
```




**Dart**
```dart 
final store = Store.instance;

await store.msgAddTodo('Learn Rid');

for (final todo in store.todos) {
    print("${todo.title}");
}
```

**Flutter**
```dart, hl_lines = 2 7 12 15 16
void main() {
  final store = Store.instance;
  runApp(TodoApp(model));
}
// [ .. ]
class TodosView extends StatelessWidget {
  final Store store;
  TodosView(this.store, {Key? key}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    final todos = store.todos;
    return Center(
      child: ListView.builder(
      itemCount: todos.length,
      itemBuilder: (_, index) => Text('${todos[index].title}'),
    ));
  }
}
```

👉   If you like to look at more detailed code samples head on over to the [rid-examples
repository](https:://gitub.com/thlorenz/rid-examples).

## Get Involved 

Please [sponsor _Rid_ development](https://github.com/sponsors/thlorenz) so it can evolve and stay maintained. 

## Contributing

Find out how to contribute to _Rid_. [Contributing →](../../contributing/how-to-contribute/)
