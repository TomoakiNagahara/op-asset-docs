# Supported Web Servers

The ONEPIECE Framework supports multiple web server environments.

Supported examples include:

- Apache
- Nginx
- LiteSpeed
- PHP built-in web server

This means the framework is not limited to a single web server implementation.

## Not Supported

The ONEPIECE Framework does not support FrankenPHP.

FrankenPHP is gaining adoption and is no longer a niche choice, but it is still not the standard execution base compared with traditional PHP-FPM-based environments.

The reason is that, in worker-style operation, static variables may remain in the process across requests.

That behavior does not match the framework assumptions for request lifecycle and state handling.

Even so, there is interest in supporting FrankenPHP in the future.
