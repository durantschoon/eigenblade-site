# eigenblade.ninja

Durant Schoon's personal site: a front page, with the essay *On the Sign of the Outer Product* featured.

An *eigenblade* is a blade that a linear map sends to a multiple of itself: an invariant subspace, in geometric algebra's language.

## Files

- `index.html`: the front page.
- `on-the-sign.html`: the essay. It is served at `/on-the-sign`. Its source of truth is `docs/web/on-the-sign.html` in the wedgeGA-symbols repo.
- `favicon.svg`: the `\wedgeGA` glyph.
- `_headers`: Cloudflare Pages response headers. For now it sets `X-Robots-Tag: noindex` on every page.

## Deploy (Cloudflare Pages, direct upload)

```sh
npx wrangler login                     # once; opens the browser
npx wrangler pages deploy . --project-name eigenblade
```

The custom domain is attached once, in the dashboard: Workers & Pages → eigenblade → Custom domains → `eigenblade.ninja`.

## LAUNCH CHECKLIST: make the site visible to search engines

The site is public but **hidden from search engines** until the announcement plan says go. This is timed with the author's academic goals.

1. Delete the `<meta name="robots" content="noindex">` line from **every** `.html` file:
   `grep -rn 'name="robots"' *.html`
2. Delete the `X-Robots-Tag: noindex` rule from `_headers` (or delete the file).
3. Redeploy: `npx wrangler pages deploy . --project-name eigenblade`
4. Check that both are gone:
   `curl -sI https://eigenblade.ninja | grep -i robots` should print nothing, and
   `curl -s https://eigenblade.ninja | grep -i 'name="robots"'` should print nothing.
5. Optional: add the site in Google Search Console and request indexing.

Both mechanisms are there on purpose: the meta tag covers the HTML pages, and the header also covers anything else that gets served.
