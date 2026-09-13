# Handoff: 2026-09-13

The site is live. Section 1 was finished on the desktop the same evening, and what changed from the phone's plan is recorded there. The open items start in section 4.

If you are a new Claude session, read this file first. Then read `FOUNDATION.md` and `docs/NOTES.md` on the `foundation` branch of `durantschoon/wedgeGA-symbols`.

---

## 1. Deploy eigenblade.ninja: done

Live at https://eigenblade.ninja and https://www.eigenblade.ninja, still noindexed. https://durantschoon.github.io forwards there, and its 404 page keeps the path, so an old link lands on the matching page.

Where the desktop run departed from the plan:

- **Token secret.** Set from the terminal, not the GitHub web page: a token from Cloudflare's *Edit Cloudflare Workers* template, then `pbpaste | gh secret set CLOUDFLARE_API_TOKEN -R durantschoon/eigenblade-site`.
- **Custom domains.** Not attached in the dashboard. They are declared as `routes` with `custom_domain: true` in `wrangler.jsonc`, and every deploy attaches them, DNS and HTTPS included.
- **workers.dev.** Declaring the routes turned off the `eigenblade.durant-schoon.workers.dev` preview URL, which leaves one canonical address. `"workers_dev": true` in `wrangler.jsonc` brings it back.
- **Bot Fight Mode.** It was on for the zone and served every request a "Just a moment..." challenge (`403`, `cf-mitigated: challenge`). A browser passes it; curl and link previews (LinkedIn, Slack, email) do not. It is now off, under Security → Settings. If the challenge ever returns, look there first.
- **Forwarder.** Branch `forwarder` of `durantschoon.github.io` is merged into `master` and live.

To verify at any time:

```sh
curl -sI https://eigenblade.ninja | grep -iE '^HTTP|x-robots-tag'          # 200 + "x-robots-tag: noindex"
curl -s  https://eigenblade.ninja/on-the-sign | grep -c 'name="robots"'     # 1
curl -sL https://durantschoon.github.io | grep -o 'eigenblade.ninja' | head -1
```

If curl answers "Could not resolve host" while `dig eigenblade.ninja` returns addresses, the Mac's resolver cached the name before the domain existed. Clear it with `sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder`.

---

## 2. Where everything is

| Repo | Branch | State |
|---|---|---|
| `wedgeGA-symbols` | `foundation` | Pushed. The essay, workshop note, THE-CASE fixes, licenses, web page source (`docs/web/on-the-sign.html`) and NOTES. Not merged into `main` yet. |
| `eigenblade-site` | `main` | Pushed and live. The site is in `public/`. `.github/workflows/deploy.yml` deploys every push to `main`, and `wrangler.jsonc` attaches both domains. |
| `durantschoon.github.io` | `master` | Live forwarder to eigenblade.ninja. The `forwarder` branch is merged and can be deleted. |

The shareable essay page on claude.ai (private until shared from its menu): https://claude.ai/code/artifact/4606980a-475f-44ba-8bf1-6c0d57b341c1

---

## 3. Decisions already made

- **Glyph meaning:** the wedgeGA glyphs name the *algebraic* operation, and plane-based PGA is stated openly as the cost.
- **Licenses:** LPPL 1.3c for `wedgeGA.sty` and the examples; CC BY 4.0 for the docs, excluding the Nolte-derived files.
- **Legacy-sign color:** orange (`#B35500` light, `#FFB65C` dark), chosen as color-blind-safe against the blue. Captions also name the mark's position.
- **Search engines:** the site is public but **noindexed**, by a meta tag on each page plus `X-Robots-Tag` in `public/_headers`. Lifting it is timed with the academic and announcement plan. The steps are the LAUNCH CHECKLIST in `README.md`.
- **Deploys:** through GitHub Actions. Cloudflare's own GitHub app install loops on a GitHub 404, at `…/installations` without the `/new`. Custom domains live in `wrangler.jsonc`, not in dashboard state.
- **Front page Work list:** gafro-extended is removed, superseded by the gafro family.
- **W for Wedge, M for Mountain:** the mosquito-and-mountain-climber joke stays out of the essay's hero captions, which name the operations. Its homes are the README, the `.sty`, and talks. The GA twist: crossing with the wedge gives a scaled mosquito (grades 1 + 0 = 1), and crossing with the mountain still gives nothing (1 + 0 − n < 0).

---

## 4. Open items, in rough priority

1. **Front page edits:** the intro in `public/index.html` is marked `EDIT ME`. Rewrite it in your voice, and add the contact links you want employers to see (LinkedIn, CV, email). With gafro-extended gone, nothing on the page credits Idiap's [gafro](https://github.com/idiap/gafro); consider a clause in the gafro family entry.
2. **The reminder to lift noindex:** pick a rough date, or ask for a scheduled check-in.
3. **Announcement plan:** state the academic goals (paper? talk? CTAN?), then draft a dated sequence: settle content, arXiv, CTAN, announce. Nothing gets posted anywhere without a per-venue go-ahead.
4. **gafro-lean going public:** discussed, not decided. The natural moment is the bridge paper's arXiv date or the Mathlib Zulip thread, since either needs the source visible. Apache-2.0 would match Mathlib for upstreaming, while the F* kernels stay GPL + commercial as the product.
5. **Nolte files** in wedgeGA-symbols `docs/`: `ExteriorAlgebraArticle.*` and the `*form*.png` figures are copied or derived from David Nolte's blog, which carries no license. Choose: replace them with your own examples (recommended), ask him for permission (he's a Purdue physicist), or cut them to a short quoted excerpt with a link.
6. **wedgeGA package:**
   - Glyph size in subscripts: about 1.3pt wider than `\wedge`, which may be deliberate.
   - The root `Makefile` works only from `examples/`.
   - CTAN wants a doc PDF.
   - `\mountainGA`: uncomment the alias in `wedgeGA.sty`, and write the punchline into its comment? Asked, not answered.
   - Merge `foundation` into `main` when you're happy with it.
7. **Research leads, unread:** the typography of Barnabei–Brini–Rota 1985 and of Bourbaki's *Algèbre* ch. III (1948).
