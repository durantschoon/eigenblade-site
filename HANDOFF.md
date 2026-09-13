# Handoff: 2026-09-13

Picking up on the desktop. Only one step is blocked: adding the Cloudflare token secret, which kept failing from the phone. Everything after it is quick.

If you are a new Claude session, read this file first. Then read `FOUNDATION.md` and `docs/NOTES.md` on the `foundation` branch of `durantschoon/wedgeGA-symbols`.

---

## 1. Finish deploying eigenblade.ninja (about 10 minutes)

**1a. Make a Cloudflare API token.** If a token from the phone attempts exists, delete it in the same place first.
dash.cloudflare.com/profile/api-tokens → **Create Token** → **Edit Cloudflare Workers** → **Use template** → Account Resources: *your account* → **Continue to summary** → **Create Token** → copy it.

**1b. Save it as a GitHub secret.** A desktop browser avoids the GitHub Mobile 2FA bounce that broke this on the phone.
github.com/durantschoon/eigenblade-site/settings/secrets/actions → **Secrets** tab → **New repository secret**
- Name: `CLOUDFLARE_API_TOKEN`
- Secret: the token

`CLOUDFLARE_ACCOUNT_ID` is already set. Check with `gh secret list -R durantschoon/eigenblade-site`: both names should be listed.

**1c. Deploy and watch.**

```sh
gh workflow run deploy.yml -R durantschoon/eigenblade-site
gh run watch -R durantschoon/eigenblade-site --exit-status
gh run view -R durantschoon/eigenblade-site --log | grep -i 'workers.dev'   # the preview URL
```

**1d. Attach the domain (dashboard, once).**
Workers & Pages → **eigenblade** → Settings → **Domains & Routes** → Add → **Custom domain** → `eigenblade.ninja`. Optionally add `www.eigenblade.ninja` too. DNS and HTTPS set themselves up, since the zone is on Cloudflare.

**1e. Verify.**

```sh
curl -sI https://eigenblade.ninja | grep -iE '^HTTP|x-robots-tag'          # 200 + "x-robots-tag: noindex"
curl -s  https://eigenblade.ninja/on-the-sign | grep -c 'name="robots"'     # 1
```

**1f. Switch the old github.io page to the forwarder.** Do this only after 1e passes, so it never forwards to a dead domain.
The forwarder is on branch `forwarder` of `durantschoon/durantschoon.github.io`. The live Pages branch is `master`, which still holds the empty 2017 Jekyll blog.

```sh
# on a machine without the clone:
gh repo clone durantschoon/durantschoon.github.io && cd durantschoon.github.io
git merge --ff-only origin/forwarder && git push origin master
# then, after a minute or two:
curl -sL https://durantschoon.github.io | grep -o 'eigenblade.ninja' | head -1
```

---

## 2. Where everything is

| Repo | Branch | State |
|---|---|---|
| `wedgeGA-symbols` | `foundation` | Pushed. The essay, workshop note, THE-CASE fixes, licenses, web page source (`docs/web/on-the-sign.html`) and NOTES. Not merged into `main` yet. |
| `eigenblade-site` | `main` | Pushed. The site is in `public/`; `wrangler.jsonc` and `.github/workflows/deploy.yml`. Waiting on the token secret. |
| `durantschoon.github.io` | `forwarder` | Pushed to a side branch only. `master` (live) is untouched. See 1f. |

The shareable essay page on claude.ai (private until shared from its menu): https://claude.ai/code/artifact/4606980a-475f-44ba-8bf1-6c0d57b341c1

---

## 3. Decisions already made

- **Glyph meaning:** the wedgeGA glyphs name the *algebraic* operation, and plane-based PGA is stated openly as the cost.
- **Licenses:** LPPL 1.3c for `wedgeGA.sty` and the examples; CC BY 4.0 for the docs, excluding the Nolte-derived files.
- **Legacy-sign color:** orange (`#B35500` light, `#FFB65C` dark), chosen as color-blind-safe against the blue. Captions also name the mark's position.
- **Search engines:** the site is public but **noindexed**, by a meta tag on each page plus `X-Robots-Tag` in `public/_headers`. Lifting it is timed with the academic and announcement plan. The steps are the LAUNCH CHECKLIST in `README.md`.
- **Deploys:** through GitHub Actions. Cloudflare's own GitHub app install loops on a GitHub 404, at `…/installations` without the `/new`.

---

## 4. Open items, in rough priority

1. **Finish section 1** (deploy, domain, forwarder).
2. **Front page edits:** the intro in `public/index.html` is marked `EDIT ME`. Rewrite it in your voice, and add the contact links you want employers to see (LinkedIn, CV, email).
3. **The reminder to lift noindex:** pick a rough date, or ask for a scheduled check-in.
4. **Announcement plan:** state the academic goals (paper? talk? CTAN?), then draft a dated sequence: settle content, arXiv, CTAN, announce. Nothing gets posted anywhere without a per-venue go-ahead.
5. **Nolte files** in wedgeGA-symbols `docs/`: `ExteriorAlgebraArticle.*` and the `*form*.png` figures are copied or derived from David Nolte's blog, which carries no license. Choose: replace them with your own examples (recommended), ask him for permission (he's a Purdue physicist), or cut them to a short quoted excerpt with a link.
6. **wedgeGA package:**
   - Glyph size in subscripts: about 1.3pt wider than `\wedge`, which may be deliberate.
   - The root `Makefile` works only from `examples/`.
   - CTAN wants a doc PDF.
   - Merge `foundation` into `main` when you're happy with it.
7. **Research leads, unread:** the typography of Barnabei–Brini–Rota 1985 and of Bourbaki's *Algèbre* ch. III (1948).
