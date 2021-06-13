# Security Headers for Cloudflare Worker

There are headers like **Content Security Policy**, **Strict Transport Security**, **Referrer Policy** and several more. Each of them serve a specific function and allow you as a website operator to ensure a safer browsing experience for your visitors.

To set a security header on one of your responses you generally need to be able to access server configuration or possibly application code to insert it from there. These are levels of access that are not always available to you on certain hosting platforms.

Cloudflare workers allow you to deploy and run code at Cloudflare's edge to apply custom processing to requests and responses to your site.

This a really simple Cloudflare worker but it enables a huge amount of security.
