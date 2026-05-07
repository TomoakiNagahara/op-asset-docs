# `OP()` Philosophy

## Overview

`OP()` is one of the most frequently used entry points in practical ONEPIECE Framework development.

It is designed to let developers call framework features from a single unified access point.

## Background

The background of `OP()` is practical developer ergonomics.

Without a unified entry point, calling core functions or unit functions across different namespaces becomes noisy and repetitive.

Developers would need to care more about:

- current class namespace
- target class namespace
- fully-qualified class names
- repeated static or object access patterns

## Purpose

The purpose of `OP()` is to make framework usage simple and uniform.

With `OP()`, developers can call framework features without having to think about class namespaces every time.

This makes it easier to:

- use core features from anywhere
- use unit features from anywhere
- keep code short and readable
- reduce namespace-related friction in daily work

## Design Meaning

In practical terms, `OP()` acts as a unified gateway to the framework.

That is why it is such an important part of the developer experience in the ONEPIECE Framework.
