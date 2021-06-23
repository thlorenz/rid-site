+++
title = "Main Page Do Not Publish"
description = "Main Page Snippets"
date = 2021-05-13
updated = 2021-05-13
draft = true
weight = 1
sort_by = "weight"
template = "docs/page.html"
+++

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

```rust
#[rid::store]
#[rid::structs(Todo)]
pub struct Store {
    todos: Vec<Todo>,
}
impl rid::RidStore<Msg> for Store {
     fn create() -> Self {
        Self { todos: vec![Todo { title: "Try Rid".to_string() }] }
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
```
