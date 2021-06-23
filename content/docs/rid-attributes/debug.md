+++
title = "rid::debug"
description = "Explains the Rid debug attribute."
date = 2021-05-13
updated = 2021-05-13
draft = false
weight = 40
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "Explains the Rid debug attribute."
toc = true
top = false
+++

## Models that `#[derive(Debug)]`

If a _struct_ or _enum_ is annotated with `#[rid::model]` or `#[rid::store]` _rid_
automatically detects if it _derives_ `Debug` via `#[derive(Debug)]`.

I such a _derive_ is found then _rid_ generates the following method for the _raw_ Dart type
representing that Rust type.

```dart
String debug([bool pretty = false]) { 
  // Call to Rust to obtain the Debug respresentation
}
```

- passing `true` for _pretty_ corresponds to `format!("{:#?}", instance)` to get a more verbose
  and readable _String_ representation of the _struct_ instance
- passing `false` or omitting _pretty_ corresponds to `format!("{:?}", instance)` to get a less
  verbose_String_ representation of the _struct_ instance

```dart
print(Store.instance.debug(true));
```

### Inclusion in Dart API

This `debug` method is not included in the Dart class generated for such _structs_ as memory
safety cannot be guaranteed since the _pointer_ to the _struct_ could become invalid from one
call to the next.

For the _Store_ however this _debug_ method is included in the recommended Dart API as well since in
this case it is memory safe since the _Store_ pointer will never become invalid while the app
is running.
