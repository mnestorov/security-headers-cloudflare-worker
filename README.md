# Security Headers for Cloudflare Worker

There are headers like **[Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)**, **[Strict Transport Security](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)**, **[Referrer Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy)** and several more. Each of them serve a specific function and allow you as a website operator to ensure a safer browsing experience for your visitors.

To set a security header on one of your responses you generally need to be able to access server configuration or possibly application code to insert it from there. These are levels of access that are not always available to you on certain hosting platforms.

Cloudflare workers allow you to deploy and run code at Cloudflare's edge to apply custom processing to requests and responses to your site.

This a really simple Cloudflare worker but it enables a huge amount of security.


```javascript
let securityHeaders = {
	'Content-Security-Policy': 'upgrade-insecure-requests',
	'Strict-Transport-Security': 'max-age=1000',
	'X-Xss-Protection': '1; mode=block',
	'X-Frame-Options': 'DENY',
	'X-Content-Type-Options': 'nosniff',
	'Referrer-Policy': 'strict-origin-when-cross-origin',
}

let sanitiseHeaders = {
	Server: 'My New Server Header!!!',
}

let removeHeaders = ['Public-Key-Pins', 'X-Powered-By', 'X-AspNet-Version']

addEventListener('fetch', (event) => {
	event.respondWith(addHeaders(event.request))
})

async function addHeaders(req) {
	let response = await fetch(req)
	let newHdrs = new Headers(response.headers)

	if (newHdrs.has('Content-Type') && !newHdrs.get('Content-Type').includes('text/html')) {
		return new Response(response.body, {
			status: response.status,
			statusText: response.statusText,
			headers: newHdrs,
		})
	}

	Object.keys(securityHeaders).map(function (name, index) {
		newHdrs.set(name, securityHeaders[name])
	})

	Object.keys(sanitiseHeaders).map(function (name, index) {
		newHdrs.set(name, sanitiseHeaders[name])
	})

	removeHeaders.forEach(function (name) {
		newHdrs.delete(name)
	})

	return new Response(response.body, {
		status: response.status,
		statusText: response.statusText,
		headers: newHdrs,
	})
}

```

## Useful links

[More information about Security Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers#security)

[Content Security Policy (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

[Strict-Transport-Security](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security)

[Referrer-Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy)

## Articles

[Introducing Cloudflare Workers: Run JavaScript Service Workers at the Edge](https://blog.cloudflare.com/introducing-cloudflare-workers/)
