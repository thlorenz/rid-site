+++
title = "rid::message and rid::reply"
description = "Explains the Rid message and reply attributes."
date = 2021-05-13
updated = 2021-05-13
draft = false
weight = 30
sort_by = "weight"
template = "docs/page.html"

[extra]
lead = "Explains the Rid message and reply attributes."
toc = true
top = false
+++

## What is it?

A Rust enum can be annotated with the `#[rid::reply]` attribute. 

This same enum can then be used when annotating another enum with the `#[rid::message(Reply)]`
attribute. 

This is how _Messages_ and _Replies_ are setup for a _rid_ application.

## How Rid sends Messages

TODO complete

## How Rid receives Replies

TODO complete
