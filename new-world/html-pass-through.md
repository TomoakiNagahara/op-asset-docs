# HTML Pass-Through

## Overview

The term **HTML Pass-Through** is still used in the ONEPIECE Framework for historical reasons.

It remains the current name because the original starting point was a controller-less way to output HTML files through the framework.

Over time, the same idea was extended and reused for other resource types such as:

- JavaScript
- CSS
- SVG
- other text-oriented resources

## Technical Meaning

At the technical level, the idea is this:

- a request may resolve directly to a resource file
- that file may still be handled through the framework flow
- the App unit executes the endpoint
- the framework decides later whether layout should be applied

This is closely related to the NEW WORLD execution model.

## Design Background

The original design goal was to allow PHP scripts to run inside HTML files without requiring a traditional controller layer.

That gave the framework a controller-less HTML entry model.

Later, the same mechanism became useful for other resource types as well.

This made it possible to apply framework features to more than HTML, including:

- dynamic response generation
- endpoint-side layout control
- header and cookie handling before body output
- resource-specific rendering behavior

Another important background is URL routing.

Many traditional frameworks tied URL segments to:

- class names
- method names

That was easy to implement, but it made operation heavier.

Even a simple page often required:

- an empty class
- an empty method

and deeper nested structures became awkward.

HTML Pass-Through changed that.

Because file-oriented endpoints can be executed directly through the framework flow:

- simple pages do not need empty controller ceremony
- `index.php` can act as the controller-style endpoint
- nested directory structures remain natural

This is one of the reasons HTML Pass-Through is not just a convenience feature.

In the current framework understanding, it is one of the three essential elements of NEW WORLD.

## Historical Development

The historical order is:

1. HTML was the original target
2. the framework used that model for controller-less HTML output
3. the same mechanism was then reused for JS, CSS, SVG, and related resource types
4. the term `HTML Pass-Through` remained even after the scope became broader

Because of that history, the current name is historically accurate, but technically narrower than the current usage.

## [DOC-GAP] Current Implementation Gap

There are several mismatches between the historical term, the current idea, and the current implementation.

### 1. The name is narrower than the current scope

The term says `HTML`, but the practical idea now reaches beyond HTML.

### 2. The Router extension list is broader than the name

The current router pass-through list includes:

- `html`
- `css`
- `js`
- `txt`
- `png`
- `ico`

This is already broader than `HTML`.

### [DOC-GAP] 2b. The extension list is hard-coded

The current pass-through extension list is hard-coded in the router implementation.

That is not a good fit with the broader framework idea.

If the pass-through target set is part of framework policy, it should be separated into configuration rather than fixed in source code.

### [DOC-FUTURE] 2c. The pass-through target set should move to config

The long-term direction should be:

- keep the current As-Is behavior documented
- move pass-through extension control into configuration
- keep the implementation aligned with the broader framework idea

### 3. Apache routing and Router behavior are not identical in scope

Under the current Apache `.htaccess`:

- existing `html`, `php`, `js`, `css`, and `txt` files are routed to `app.php`
- non-existent files are also routed to `app.php`
- existing image files are normally served statically

That means the Router implementation and the current Apache routing policy are not identical in effective behavior.

### 4. The name does not fully express the broader resource-oriented intent

The framework now uses the same conceptual flow for more than HTML, but the historical name has not yet been updated.

## Philosophy

Even with that terminology gap, the underlying philosophy is consistent:

- resources should be able to participate in the framework flow
- endpoint execution and final rendering should be separated
- layout must be controllable dynamically
- framework control should be available where it adds value

In this broader meaning, HTML Pass-Through is not just about HTML files.

It is also part of the **Grand Line** of NEW WORLD:

- reducing unnecessary controller ceremony
- keeping routing file-oriented
- allowing natural nesting
- letting simple pages and richer application endpoints coexist

## [DOC-FUTURE] Future Direction

The current mismatch between:

- historical naming
- technical scope
- current implementation details

is recognized as something to improve over time.

The current policy is to keep using the historical name `HTML Pass-Through` for now, while documenting the broader practical meaning and the implementation differences clearly.

The long-term direction is to resolve this gap more cleanly in future framework evolution.
