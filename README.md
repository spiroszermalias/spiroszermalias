<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/banner-dark.svg">
  <source media="(prefers-color-scheme: light)" srcset="assets/banner-light.svg">
  <img alt="Spiros Zermalias, Software & Systems Engineer" src="assets/banner-light.svg" width="100%">
</picture>

<p align="center">
  <a href="https://spiroszermalias.com">spiroszermalias.com</a> &nbsp;·&nbsp;
  <a href="https://digen.gr">digen.gr</a> &nbsp;·&nbsp;
  <a href="https://www.linkedin.com/in/spiros-zermalias/">LinkedIn</a> &nbsp;·&nbsp;
  <a href="https://x.com/SZermalias">X</a> &nbsp;·&nbsp;
  <a href="mailto:lets.chat@digen.gr">lets.chat@digen.gr</a>
</p>

I run [Digital Engine](https://digen.gr), a small hosting and engineering practice in Athens. Most of
what I build is the infrastructure my clients' sites run on, plus the tooling around it: DDoS detection,
caching, server monitoring, ERP integrations, and a few LLM pipelines that extract structured data from
messy sources.

Nearly all of it lives in private repositories, so there is little to click on here. The projects
below are described rather than linked. If you want to see the code, get in touch and I can walk you through it.

## Selected work

### Phrouros
Layer 7 DDoS detection and Cloudflare mitigation, packaged as a Plesk extension. <br><sub>Python, PHP, CloudLinux LVE, Cloudflare API, SQLite, Grafana</sub>

Application-layer attacks are made of valid requests, so nothing at the network level catches them and
the first symptom is usually a slow site. Phrouros learns a baseline for each site from its own history
(median and MAD), and flags an anomaly only when the traffic pattern in the access log and the resource
usage reported by CloudLinux drift away from that baseline at the same time. It then checks Cloudflare's
analytics and applies a Managed Challenge to the offending traffic only. It doesn't block anything, and
each site starts in shadow mode until I switch it on.

In production it has cut attack traffic by about 70% on average.

### AI Content Layout Engine
Rebuilds messy WooCommerce product descriptions into a consistent layout, with a person approving every change. <br><sub>PHP 8.1, WordPress, WooCommerce, OpenAI API, Action Scheduler, WP-CLI</sub>

I built it first for a store with about 70,000 products whose descriptions had been written by hand over
many years: inline styles, long blocks of text, markup pasted in from other sites. A migration script
couldn't cope with input that inconsistent, so the plugin adds an "AI Suggestion" button to each product.

The model never writes the page's HTML. It returns JSON that fills the named slots of a layout template
written by a developer, using a fixed set of components, and the plugin validates that and renders the
markup itself. The output stays consistent and sanitised, and after a design change every product can be
re-rendered from its stored data without calling the API again. The model can add a section the template
doesn't have only when real content fits nowhere else, and it has to give a reason.

Enrichment is optional per product: the model can research missing specs online, preferring manufacturer
sources and citing what it added, and it can suggest product images, which only enter the media library
once approved. Text and images are approved separately, nothing publishes on its own, descriptions edited
by hand are never overwritten without an explicit override, and every change can be rolled back. Each
task can use a different model, and the plugin tracks token cost per run. A layout costs about $6 per
thousand products.

### Redis/APCu Object Cache
A WordPress object cache with three layers. <br><sub>PHP 8.1, Redis, APCu, PHPUnit, PHPStan, Docker</sub>

APCu in-process is the first layer, Redis the second, and MySQL the fallback. The first layer doesn't
depend on Redis, so a site keeps serving from APCu when Redis goes down. It comes in a free and a paid
edition built on the same core. The WordPress cache API behaviour is covered by a conformance suite
that runs in Redis-only, APCu-only and tiered configurations, against real Redis and APCu, with failures
injected on purpose.

### Page Cache Manager
Keeps an nginx `proxy_cache` fresh for WordPress sites on Plesk and CloudLinux. <br><sub>PHP, Python, nginx, systemd, WP-CLI</sub>

Open-source nginx can't purge, PHP can't read the cache directory, and CloudLinux doesn't give the site
a shell. The plugin works out which URLs a change affected (the post, its archives, the front page, shop
pages, Elementor's CSS) and re-requests them with a token that makes nginx bypass and overwrite the cached
copy. Large changes go to a companion Plesk extension, where a systemd unit running as the nginx user
invalidates the matching cache files. About 800 tests across unit, integration and artifact suites.

### Understudy
Load tests a prospect's current host and recommends one of my hosting plans. <br><sub>Python, k6, Node.js, Express, SQLite, n8n</sub>

You give it a domain. It gets past the CDN to the origin, detects the platform (WordPress, WooCommerce,
Laravel, Node), crawls a weighted set of URLs and runs a k6 load test. The results are compared against
benchmarks I ran on my own hosting tiers, and the report goes out as a web page and a webhook into the CRM.

### Other work
- Bridge 4 ERP, a WordPress plugin that connects WooCommerce to any ERP with a REST or SOAP web service.
  The ERP sits behind an interface, so the store side stays the same whichever ERP is on the other end.
  I also built a SoftOne (S1) integration that sends orders and new customers to the ERP and brings
  product details and stock back. At digital challenge_ this integration work halved turnaround time.
- OppScout, a pipeline that collects Erasmus+ youth opportunities from many sources, parses their PDF
  infopacks, has an LLM extract the details in English and Greek, and publishes them to WordPress.
  Duplicates across sources are caught before anything is sent to the model.
- IEP Voucher, a tool for distributing Panhellenic exam test papers, with a SOAP integration.
- A Cloudflare cache preloader that warms pages from sitemaps and tracks `cf-cache-status` per URL.

## Public repositories

| Repository | What it does |
| --- | --- |
| [common-dev-container](https://github.com/spiroszermalias/common-dev-container) | Dev container with a default-deny firewall for running coding agents |
| [autofill-excluder](https://github.com/spiroszermalias/autofill-excluder) | Chromium extension that turns off autofill on sites you choose |
| [url-decoder](https://github.com/spiroszermalias/url-decoder) | Chromium extension that copies URLs in decoded form |

## How I work

I write down the reasons behind design decisions. Phrouros, the object cache and Page Cache Manager
have about 270 decision records between them. I test against real Redis, MariaDB and nginx rather than mocks wherever I can,
and anything that can affect a live site runs in a dry-run mode before it is allowed to act. I use
Claude Code a lot, with project documentation it has to follow, and I review what it changes.

## Tools

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://skillicons.dev/icons?i=py%2Cphp%2Cgo%2Cjs%2Cnodejs%2Claravel%2Cwordpress%2Clinux%2Cbash%2Cnginx%2Cdocker%2Ccloudflare%2Credis%2Cmysql%2Csqlite%2Cgrafana%2Cselenium%2Cgit%2Cgithubactions&perline=19&theme=dark">
  <source media="(prefers-color-scheme: light)" srcset="https://skillicons.dev/icons?i=py%2Cphp%2Cgo%2Cjs%2Cnodejs%2Claravel%2Cwordpress%2Clinux%2Cbash%2Cnginx%2Cdocker%2Ccloudflare%2Credis%2Cmysql%2Csqlite%2Cgrafana%2Cselenium%2Cgit%2Cgithubactions&perline=19&theme=light">
  <img alt="Python, PHP, Go, JavaScript, Node.js, Laravel, WordPress, Linux, Bash, nginx, Docker, Cloudflare, Redis, MySQL, SQLite, Grafana, Selenium, Git, GitHub Actions" src="https://skillicons.dev/icons?i=py%2Cphp%2Cgo%2Cjs%2Cnodejs%2Claravel%2Cwordpress%2Clinux%2Cbash%2Cnginx%2Cdocker%2Ccloudflare%2Credis%2Cmysql%2Csqlite%2Cgrafana%2Cselenium%2Cgit%2Cgithubactions&perline=19&theme=light">
</picture>

Plus Plesk and CloudLinux administration, REST and SOAP APIs, k6 and n8n.

## Other things

I founded [U-Grow](https://u-grow.gr), a youth education organisation that runs Erasmus+ projects and
exchanges, and I'm still its legal representative. At university I was sponsorship manager for
TEDxUniversityofCrete and a project manager at GreenTeam UoC, and my team won first prize at Startup Weekend
Heraklion in 2017 with a VR app for public speaking anxiety. My degree is in Materials Science and
Engineering from the University of Crete.

I speak Greek, English and some French.
