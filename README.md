# eigenblade.ninja

Durant Schoon's personal site: a front page, with the essay *On the Sign of the Outer Product* featured.

An *eigenblade* is a blade that a linear map sends to a multiple of itself: an invariant subspace, in geometric algebra's language.

## Files

The site lives in `public/`, which is Cloudflare Pages' output directory. This README stays outside it, so it is never served.

- `public/index.html`: the front page.
- `public/on-the-sign.html`: the essay. It is served at `/on-the-sign`. Its source of truth is `docs/web/on-the-sign.html` in the wedgeGA-symbols repo.
- `public/favicon.svg`: the `\wedgeGA` glyph.
- `public/_headers`: Cloudflare Pages response headers. For now it sets `X-Robots-Tag: noindex` on every page.

## Deploy

Every push to `main` deploys through GitHub Actions (`.github/workflows/deploy.yml`) to Cloudflare, as a Worker with static assets. `wrangler.jsonc` sets the name `eigenblade` and the assets folder `./public`. `_headers` applies there as it would on Pages.

The workflow needs two repository secrets, set under GitHub → Settings → Secrets and variables → Actions:

- `CLOUDFLARE_API_TOKEN`: a Cloudflare API token made from the *Edit Cloudflare Workers* template, which also covers the routes that attach the custom domains.
- `CLOUDFLARE_ACCOUNT_ID`: shown in the Cloudflare dashboard, on the Workers & Pages overview.

Until both are set, the workflow skips the deploy rather than failing. To run it by hand: `gh workflow run deploy.yml`.

The custom domains, `eigenblade.ninja` and `www.eigenblade.ninja`, are declared as `routes` in `wrangler.jsonc`, and every deploy attaches them, DNS and HTTPS included. Declaring them turns off the `workers.dev` URL; `"workers_dev": true` brings it back. Bot Fight Mode is off for the zone, because its challenge page blocks link previews.

Why Actions rather than Cloudflare's own Git integration: on 2026-09-13 its GitHub app install kept looping to a GitHub 404, at `…/installations` without the `/new`.

## LAUNCH CHECKLIST: make the site visible to search engines

The site is public but **hidden from search engines** until the announcement plan says go. This is timed with the author's academic goals.

1. Delete the `<meta name="robots" content="noindex">` line from **every** `.html` file:
   `grep -rn 'name="robots"' public/`
2. Delete the `X-Robots-Tag: noindex` rule from `public/_headers` (or delete the file).
3. Commit and push to `main`; the Deploy workflow redeploys automatically.
4. Check that both are gone:
   `curl -sI https://eigenblade.ninja | grep -i robots` should print nothing, and
   `curl -s https://eigenblade.ninja | grep -i 'name="robots"'` should print nothing.
5. Optional: add the site in Google Search Console and request indexing.

Both mechanisms are there on purpose: the meta tag covers the HTML pages, and the header also covers anything else that gets served.
