> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [tabserve.dev](https://tabserve.dev/)

> Tabserve

A secure & fast HTTPS URL for localhost using your browser as a reverse proxy.

![](https://tabserve.dev/_astro/screenshot-1.deb281eb.png)

Intro
-----

Tabserve is a web app that uses a single [Cloudflare worker](https://workers.cloudflare.com/) combined with browser based [web workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) to create a reverse proxy from the internet to your localhost.

This gives you an HTTPS URL on `your-domain.com` (such as `https://anything.your-domain.com`) that forwards traffic to any `localhost` address, such as `http://localhost:1234`.

You can instantly create any number of subdomains as Cloudflare provisions a TLS wildcard certificate - no waiting for a DNS propagation to take effect.

This lets you share your local web server with the world, receive webhooks, test from different devices, and use browser JS API’s that are HTTPS-only. You can also run small to medium production services from any machine with a web browser (although Tabserve is currently alpha - [please submit any issues you encounter](https://github.com/emadda/tabserve/issues/new)).

Your local web service is protected and optimized by Cloudflares Web Application Firewall (WAP), and uses the latest HTTP protocols and optimizations (HTTP-3). Your CF worker will be created in the CF location nearest to you in one of [300 global locations](https://www.cloudflare.com/en-gb/network/) which reduces latency and speeds up instant reloading during development.

Each subdomain currently maps to a single [Durable Object](https://developers.cloudflare.com/workers/learning/using-durable-objects/). This gives you a throughput of between 100-500 RPS (informally tested), and isolates each subdomain from each other for security and fault/load isolation. The [WebSocket Hibernation API](https://developers.cloudflare.com/workers/runtime-apis/durable-objects/#websockets-hibernation-api) is used, so you only get charged for the time you are forwarding requests, and should generally be less than $5/month ([pricing](https://developers.cloudflare.com/workers/platform/pricing/#durable-objects)).

Current reverse proxy tools require you to install a CLI on your machine, which gives it access to your entire machine and can be difficult to observe and configure.

Tabserve “installs” by just loading the web page (less than 300ms), and is [isolated using the Chrome browser](https://www.chromium.org/Home/chromium-security/brag-sheet/). V8 and other JS engines have some of the most robust and well tested sandboxing. Cloudflare workers also use V8, which means both the server and client of the reverse proxy have strong sandboxes. V8 is also a very fast runtime.

Note: You must upload a single small [Cloudflare worker](https://github.com/emadda/worker-tabserve-reverse-proxy) to your Cloudflare account and have a domain added to Cloudflare DNS to use Tabserve.

Interesting things you can do
-----------------------------

*   A public HTTPS URL for localhost
    *   Share your local web server with your any team members around the world.
    *   Receive web hooks from API’s to your local web server.
    *   Use web API’s that require HTTPS.
    *   Test on different devices.
*   Unlimited subdomains such as `a.your-domain.com`, `b.your-domain.com`.
    *   Each subdomain has a valid HTTPS cert instantly - no DNS or certificate set up required.
*   Use any device with a browser as a reverse proxy.
    *   iPad, iPhone etc (alpha)
    *   This allows you to route web traffic from the internet to the LAN without setting up firewall rules or installing third party proxy clients that have access to your whole computer.
    *   Forward HTTP requests to any domain, not just localhost.
*   Configure reverse proxies remotely.
    *   Each browser you leave open on a machine shows up in your Tabserve UI.
    *   Servers can be added and removed remotely.
*   Debug your server using Chrome dev tools.
    *   The Tabserve UI runs completely locally and uses your browsers `fetch` API - these show up in the network tab.
    *   Chrome dev tools is familiar and feature rich.
*   Chain reverse proxies (Tabserve -> GUI -> local web server).
    *   Use your preferred HTTP GUI to observe requests.
    *   Use Charles or another GUI to set up a reverse proxy with rule like: `localhost:1234 (Tabserve forward_to) -> localhost:5678 (your actual dev web server)`
*   Use as a stand-in for a production server.
    *   You can use `xyz.your-domain.com` in the Tabserve UI, and then switch your Cloudflare DNS to a real production web server when ready.
    *   You can use Cloudflare features for the public server URL.
        *   Latest HTTP protocol support.
        *   Web Application Firewall (WAP)
        *   On-the-fly optimizations for JS and images.
*   Use multiple root domains.
    *   You can add any number of domains to Tabserve.
*   And probably billions of other things.
*   [Let me know how you’re using Tabserve in the show and tell](https://github.com/emadda/tabserve/discussions/new?category=show-and-tell).
*   Contact [support@tabserve.dev](mailto:support@tabserve.dev) for direct help.

Sequence diagram
----------------

Setting up the Cloudflare Worker and DNS
----------------------------------------

Limitations
-----------

*   Limitations that will be fixed in future versions:
    *   Responses over 5MB will temporarily block the worker event loop and slow all requests for that particular domain (cached responses fix this).
    *   No WebSocket requests.
    *   Some browsers try to sleep/restore browser Web Worker threads - this requires more testing.
        *   Chrome and Firefox desktop browsers work fine when left open for many days.
    *   Only 100-500 RPS.
    *   No TCP/UDP connections. Tabserve is a HTTP only proxy.
    *   You must [enable CORS](https://enable-cors.org/server.html) on the localhost web server.