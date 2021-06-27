+++
title = "Multithreaded Todo App"
description = "A Todo app built with Flutter and Rust using Rid"
date = 2021-05-13
updated = 2021-05-13
draft = false
weight = 10
sort_by = "weight"
template = "docs/page.html"
+++

![img](../flutter-todo.png)

👉 Read the [source](https://github.com/thlorenz/rid-examples/tree/master/flutter/todo_cubit) of this example
as part of the [rid examples repo](https://github.com/thlorenz/rid-examples).

👉 [Get started with _Rid_](../../getting-started/introduction/) and [learn about its
architecture](../../getting-started/architecture/).

## Screencast

<iframe width="560" height="315" src="https://www.youtube.com/embed/PGKBdxOA6Xs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Here we are use Rid to easily integrate Rust with Flutter via simple annotations and
demonstrate the easy to use yet memory safe API to your Rust code which our Bloc/Cubits can
leverage.

## Walkthrough

Make sure to check back for step by step instructions on how to build this application.

[Sponsors](../../contributing/sponsor/) get early access.

<div style="opacity: 0.6;">
<h4>Older Screencast of Single Threaded Todo App</h4>
<iframe width="560" height="315" src="https://www.youtube.com/embed/Rs3_N4maNPw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

_Some of the API used is now obsolete and all apps now use the Message/Reply pattern instead of
expecting Store updates to complete synchronously._

_However it is still an interesting watch if you want to understand more about how Rid works and
how it evolved._
</div>

