# Images

## Overview

In the ONEPIECE Framework, images can also be handled through the framework flow.

This means image-related requests are not limited to static file delivery. They can be controlled by the application in the same way as other framework-managed responses.

## How It Works

Which URL executes `app.php` depends on web server configuration.

In the case of Apache, that behavior is described in `.htaccess`.

Because of that, image handling is also affected by `.htaccess` routing rules.

At the current Apache setting:

- if the requested image file does not physically exist, the request reaches `app.php`
- if the requested image file physically exists, Apache serves it statically

When a non-existent image request reaches `app.php`, the flow becomes:

1. `app.php` starts the framework
2. the endpoint `index.php` is selected and executed
3. the application can generate or alter the image response dynamically

## What This Makes Possible

This design makes it possible to:

- place image responses under framework control
- generate images dynamically
- change image output depending on the situation
- apply application-side rules before returning an image
- handle image URLs that do not correspond to a physical file
- keep ordinary existing image files as static delivery when appropriate

## Access Control Use Case

This is useful when an image should not always be shown in the same way to every requester.

For example, the application can decide whether to return an image, or which image to return, depending on application-side conditions.

That makes it possible to avoid simply exposing the same image to:

- users who are not permitted to see it
- unspecified third parties
- direct URL access without application-side judgment

## Text-Based Image Formats

Text-based image formats such as SVG are especially compatible with this model.

Because SVG is text, it can be generated or modified dynamically with PHP before being returned as a response.

## Summary

In the ONEPIECE Framework, images are not treated only as static assets.

Depending on web server routing, they can also be treated as framework-managed responses, which allows dynamic generation, conditional output, and application-controlled delivery.
