# WebPack

## Overview

One of the characteristics of the ONEPIECE Framework is that it can deliver multiple JavaScript files or multiple CSS files through a single request.

This is handled by combining:

- `op-unit-webpack`
- the `webpack` module

## Related Framework Documents

- `../op/invariants.md`
- `../op/responsibility-boundaries.md`
- `../op/common-recipes.md`

## Main Idea

The purpose is not only file collection.

The framework provides a mechanism where:

- multiple `js` files can be grouped and sent together
- multiple `css` files can be grouped and sent together
- the response can be built dynamically through framework logic

## Role Split

At a high level:

- `op-unit-webpack` manages registration, selection, and output preparation
- the `webpack` module acts as the delivery-side module for sending the grouped assets

## Why This Matters

This makes it possible to treat front-end asset delivery as part of the framework workflow rather than as a collection of unrelated static files.

That allows the framework to coordinate:

- layout-related asset grouping
- dynamic output behavior
- unified request-driven delivery
