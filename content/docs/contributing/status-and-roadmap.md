+++
title = "Status and Roadmap"
description = "Status and Roadmap of Rid"
date = 2021-05-14 
updated = 2021-05-14
draft = false
weight = 20
sort_by = "weight"
template = "docs/page.html"

[extra]
toc = true
top = false
+++

## Status

_Rid_ is functional at this point, but not _production ready_.

I have tested the [example apps](../../examples/) on macOS desktop, numerous iPhone simumlators and a physical
Android device (Pixel running Android 10).

I also took great care regarding memory management and used [valgrind](https://valgrind.org/)
to ensure that _rid_ doesn't leak memory when used properly.

_Rid_ is very well tested, especially the `rid-macro` crate which is responsible for
generating the FFI glue code.

## Roadmap

I am adding features to _rid_ by building example applications and filling in what's needed.

### ✅ Async Support 

I'm planning on building the following two applications next:

- Todo app using bloc state management
- reddit ticker which updates vote count of a post in real time

Both require to communicate state changes from Rust asynchronously which are now supported.

### 🔨👷 Support More Collection Types 

This is an ongoing effort as they will be added on a per need basis. Most likely they will be
added in the order below

- [HashSet](https://doc.rust-lang.org/std/collections/hash_set/struct.HashSet.html)
- [BTreeSet](https://doc.rust-lang.org/std/collections/struct.BTreeSet.html)
- [HashMap](https://doc.rust-lang.org/std/collections/struct.HashMap.html) 
- [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) 

Please note that supporting fields of a particular type on _model_ structs is a different task
than supporing _export_ functions to return values of same type.

The `Vec` type is currently supported for both cases.

### 🔨👷 Wasm Support

_Rid_ will support all platforms that Flutter supports. Therefore it is essential that it can
compile to wasm and generate the necessary glue code.

I did have a first stab at this, and realized there are a lot of pieces missing still,
especially on the Dart side. Therefore I put this off for a later time. For more information
please refer to to [this post](../../../blog/wasm-support.md).

### ⌛ Rid CLI Tool 

At this point _rid_ apps are created via a template which includes scripts to generate glue
code and build for different devices. They rely on the `rid-build` crate.

I will create a CLI tool which will have numerous features aiding in the development of apps with _rid_:

- **scaffold command**: to create a ready to run and work on Dart or Flutter application
- **build command**: which will create glue code and build binaries for each device 

## ⌛ Stability

_Rid_ is evolving very fast and has some gaps in the implementation. Even though most of them
will be addressed by necessity when a new example app depends on it, I want to take some time
to meticulously go through the code base and address most of them.
