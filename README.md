# Security Headers for Cloudflare Worker

[![Licence](https://img.shields.io/github/license/Ileriayo/markdown-badges?style=for-the-badge)](./LICENSE)

## Overview

Web security headers are crucial in protecting your site and its visitors from various types of attacks such as **cross-site scripting (XSS)**, **clickjacking**, and other **code injection** attacks. This repository provides a **Cloudflare Worker** script to dynamically add, modify, and remove HTTP headers for enhancing the security of any website.

There are headers like **[Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)**, **[Strict Transport Security](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)**, **[Referrer Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy)** and several more. 

Each of them serve a specific function and allow you as a website operator to ensure a safer browsing experience for your visitors.

## Description

Adjusting security headers typically requires access to server configurations or direct insertion via application code, which might not be feasible on all hosting platforms. Cloudflare Workers offers a flexible and powerful way to manipulate HTTP responses at the edge, close to your users, without needing traditional server access.

This project demonstrates how to utilize Workers to improve your site's security posture significantly by implementing recommended security practices through HTTP headers.

## Setup and Usage with Wrangler

### Installing Wrangler

To manage Cloudflare Workers, you first need to install Wrangler, Cloudflare's official CLI for working with Workers:

```bash
npm install -g @cloudflare/wrangler
```
### Authenticating Wrangler

Authenticate Wrangler with your Cloudflare account by running:

```bash
wrangler login
```
This command will open a web browser asking you to log in with your Cloudflare account and authorize Wrangler.

### Creating a Worker

Create a new Worker project by running:

```bash
wrangler generate my-security-headers-worker
cd my-security-headers-worker
```

### Developing the Worker

Edit the `index.js` and potentially the `wrangler.toml` to configure your project and specify deployment settings.

### Publishing the Worker

Once your script is ready, you can publish it to Cloudflare's edge using:

```bash
wrangler publish
```

## Examples

### Simplified example of a Cloudflare Worker script that applies several security headers

This basic setup can be expanded based on specific security needs.

```javascript
let securityHeaders = {
    'Content-Security-Policy': 'upgrade-insecure-requests',
    'Strict-Transport-Security': 'max-age=31536000; includeSubDomains; preload',
    'X-Frame-Options': 'DENY',
    'X-Content-Type-Options': 'nosniff',
    'Referrer-Policy': 'strict-origin-when-cross-origin',
}

let sanitiseHeaders = {
    Server: 'My Custom Server',
}

let removeHeaders = ['Server', 'X-Powered-By', 'X-AspNet-Version']

addEventListener('fetch', event => {
    event.respondWith(handleRequest(event.request))
})

async function handleRequest(request) {
    let response = await fetch(request)
    let newHeaders = new Headers(response.headers)

    // Apply security headers
    Object.entries(securityHeaders).forEach(([name, value]) => {
        newHeaders.set(name, value)
    });

    // Modify or add specific headers
    Object.entries(sanitiseHeaders).forEach(([name, value]) => {
        newHeaders.set(name, value)
    });

    // Remove headers that disclose sensitive information
    removeHeaders.forEach(header => {
        newHeaders.delete(header)
    });

    return new Response(response.body, {
        status: response.status,
        statusText: response.statusText,
        headers: newHeaders,
    });
}
```
### Enforcing Content Security Policy for Script Sources

This example demonstrates how to **enforce a strict CSP** by specifying allowed script sources:

```
securityHeaders['Content-Security-Policy'] = "default-src 'self'; script-src 'self' https://trustedscripts.example.com";
```

### Customizing the Server Header

Instead of revealing the server type, replace it with a generic description to obscure potential targets for attacks:

```
sanitiseHeaders['Server'] = 'SecureServer';
```

## Useful links

- [More information about Security Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers#security)
- [Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [Strict-Transport-Security](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)
- [Referrer-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy)
- [Introducing Cloudflare Workers: Run JavaScript Service Workers at the Edge](https://blog.cloudflare.com/introducing-cloudflare-workers/)

---

## License

This project is released under the MIT License.
