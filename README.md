# eigenblade.ninja

Durant Schoon's personal site: a front page, with the essay *On the Sign of the Outer Product* featured.

An *eigenblade* is a blade that a linear map sends to a multiple of itself: an invariant subspace, in geometric algebra's language.

## Files

The site lives in `public/`, which is Cloudflare Pages' output directory. This README stays outside it, so it is never served.

- `public/index.html`: the front page.
- `public/on-the-sign.html`: the essay. It is served at `/on-the-sign`. Its source of truth is `docs/web/on-the-sign.html` in the wedgeGA-symbols repo.
- `public/favicon.svg`: the `\wedgeGA` glyph.
- `public/_headers`: Cloudflare Pages response headers. For now it sets `X-Robots-Tag: noindex` on every page.

## Deploy (Cloudflare Pages, Git integration)

The Pages project `eigenblade` is connected to this GitHub repo: **every push to `main` deploys**. Its build settings are: framework preset *None*, build command empty, output directory `public`.

A manual deploy is also possible, with an API token in `CLOUDFLARE_API_TOKEN`:

```sh
npx wrangler pages deploy public --project-name eigenblade
```

The custom domain is attached once, in the dashboard: Workers & Pages → eigenblade → Custom domains → `eigenblade.ninja`.

### Alternative: deploy as a Worker (Workers Builds)

If the dashboard only offers Workers, `wrangler.jsonc` (name `eigenblade`, assets `./public`) makes the same repo deployable as a Worker with static assets. `_headers`, and so the noindex rule, works there too. Import the repo under the name `eigenblade`, leave the build command empty, and use the deploy command `npx wrangler deploy`. Attach the domain under the Worker's Settings → Domains & Routes → Custom domain.

## LAUNCH CHECKLIST: make the site visible to search engines

The site is public but **hidden from search engines** until the announcement plan says go. This is timed with the author's academic goals.

1. Delete the `<meta name="robots" content="noindex">` line from **every** `.html` file:
   `grep -rn 'name="robots"' public/`
2. Delete the `X-Robots-Tag: noindex` rule from `public/_headers` (or delete the file).
3. Commit and push to `main`; Pages redeploys automatically.
4. Check that both are gone:
   `curl -sI https://eigenblade.ninja | grep -i robots` should print nothing, and
   `curl -s https://eigenblade.ninja | grep -i 'name="robots"'` should print nothing.
5. Optional: add the site in Google Search Console and request indexing.

Both mechanisms are there on purpose: the meta tag covers the HTML pages, and the header also covers anything else that gets served.
