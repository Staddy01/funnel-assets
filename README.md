# funnel-assets

Public static assets for funnel pages, served through jsDelivr.

This repository is **public on purpose**. jsDelivr does not serve private
repositories, and the Funnel Cloning SOP (section 14) requires a public repo
behind a CDN so every asset URL can be pinned to an immutable commit SHA.

## What goes in here

Funnel page assets only: images, GIFs, and stylesheets that a published funnel
page loads.

## What must NEVER go in here

- Credentials, tokens, API keys, cookies or session files
- Customer data of any kind, including orders, emails and addresses
- Unreleased pricing, margins, supplier names or cost data
- Anything from `funnel-clone/work/`, `_tmp/` or a job directory
- Source captures of competitor pages

Assume anyone can read every file here, forever. Deleting a file later does not
un-publish what has already been fetched and cached.

## Layout

```
<product>/<market>/...      one directory per product per market
shared/...                  assets used by more than one funnel
DEPLOYED.md                 the deployed commit SHA per product and market
```

## How URLs are formed

```
https://cdn.jsdelivr.net/gh/Staddy01/funnel-assets@<commit-sha>/<path>
```

**Always pin the commit SHA. Never reference `@main` or any branch name.** A
branch reference is mutable, the CDN caches it, and purge endpoints are
best-effort — they have repeatedly served stale files after a push.

The cost of this rule is that updating an asset means updating every reference
to it. That is the intended trade: an asset URL, once published, never changes
what it serves.

## Updating

1. Add or replace the files.
2. Commit and push.
3. Record the new SHA per market in `DEPLOYED.md`.
4. Rewrite every reference to the new SHA.
5. **Warm the CDN** — request each new URL once and expect 200 — *before* any
   visual check. A cold jsDelivr URL can 404 on first request while it
   populates, which makes a render gate report broken images on a page that is
   actually fine.

The `cdn` asset lane in `funnel-clone` (`fc/assets/cdn.py`) does steps 3-5
automatically, including a byte-compared read-back with backoff.
