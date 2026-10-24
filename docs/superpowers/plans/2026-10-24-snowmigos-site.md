# Snowmigos Site Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Put `snowmigos.com` live as the Snowmigos app's own site — placeholder landing page, privacy policy, invite landing page and both app-association files — and strip the Piste Pals pages from `kuamka.com`.

**Architecture:** A new repo `kuamka/snowmigos-site`, Jekyll with `remote_theme: mmistakes/minimal-mistakes`, built by GitHub Pages, same stack and `_script` tooling as `kuamka/kuamka.github.io`. The two Piste Pals pages are ported from that repo and renamed, not rewritten. DNS is at Cloudflare and points the apex at GitHub Pages; `snowmigos.app` gets a Cloudflare redirect rule to `https://snowmigos.com`. Browser work on Cloudflare and GitHub is driven in Claude in Chrome with the user's own sign-ins.

**Tech Stack:** Jekyll 3 (github-pages gem), minimal-mistakes remote theme, Ruby 3.0.0 via rbenv, GitHub Pages, Cloudflare DNS + Redirect Rules, `gh` CLI.

**Spec:** `kuamka/piste-pals` issue #264, Phase 3 ("Website on snowmigos.com") — <https://github.com/kuamka/piste-pals/issues/264>

## Global Constraints

- Site repo is `kuamka/snowmigos-site`; default branch `master` (matches `kuamka.github.io`).
- iOS app ID: `9DG4MJ43HD.com.kuamka.snowmigos`. Android package: `com.kuamka.snowmigos`.
- URL scheme: `snowmigos://`. Invite link base: `https://snowmigos.com/join/`. Privacy policy: `https://snowmigos.com/privacy/`.
- App-link paths are `/join` and `/join/*` — **not** the old `/piste-pals/join`.
- The privacy policy must keep its `#deleting-your-data` anchor; the Play Data Safety form links to it.
- Contact addresses stay `privacy@kuamka.com` and `support@kuamka.com`. Kuamka Ltd remains the data controller; company number 07961814 and the registered office are unchanged.
- "Piste Pals" and every form of it (`piste-pals`, `pistepals`, `pistePals`, `com.kuamka.pistepals`) must not appear anywhere in the new repo. The plain word "piste" for ski runs is fine.
- No file in `.well-known/` may be served via a redirect — Apple and Google do not follow them. The apex must answer those two paths with a direct `200`.
- `assetlinks.json` ships with the **local debug** SHA-256 and a comment marking it provisional; the Play App Signing fingerprint is issue #264 Phase 1 and gets swapped in later.
- Locale `en-GB`, British spelling, single sentence copy; no trailing full stop on single-sentence UI microcopy.

## Review Focus

Checked the spec for input classes and failure modes no task's own verification covers. Five that would bite a real visitor, most likely first:

1. **`/join/?token=…` opened in a desktop browser or an in-app browser (WhatsApp, Instagram).** Universal Links never fire there, so the page itself must be useful: the open-in-app link must appear when a `token` is present and stay hidden when it is not. Pinned as a test in Task 5.
2. **A `token` carrying URL-unsafe characters.** The old page interpolated the raw value into an `href`; the port must keep `encodeURIComponent`. Pinned as a test in Task 5.
3. **`http://snowmigos.com/.well-known/…` and `www.snowmigos.com/.well-known/…`.** GitHub Pages redirects both to the apex https host. Apple and Google are only ever given the apex https URL, but a human debugging will try the others — Task 9 records what each returns so a redirect there is not mistaken for a broken file.
4. **`snowmigos.app` reached over plain `http`.** `.app` is HSTS-preloaded, so a browser upgrades it, but a `curl` or a non-browser client does not. The redirect rule must match both schemes. Pinned as a check in Task 8.
5. **Visitor landing on a path that does not exist** (`/piste-pals/privacy/` typed from an old link, say). Minimal-mistakes needs a `404.md` or GitHub Pages serves its own generic page. Pinned as a test in Task 3.

---

## Task 1: Scaffold the repo and get an empty site building

**Files:**
- Create: `~/Code/Kuamka/snowmigos-site/` — `Gemfile`, `.gitignore`, `.ruby-version`, `.bundle/config`, `_config.yml`, `CNAME`, `_script/{_jekyll,build,serve,setup}`
- Reference (copy from): `~/Code/Kuamka/kuamka.github.io/` — same filenames

**Interfaces:**
- Consumes: nothing.
- Produces: a buildable Jekyll site at `~/Code/Kuamka/snowmigos-site`, `_script/build` producing `_site/`, and a pushed GitHub repo `kuamka/snowmigos-site` with default branch `master`.

- [ ] **Step 1: Copy the build tooling verbatim**

```bash
cd ~/Code/Kuamka/snowmigos-site
src=~/Code/Kuamka/kuamka.github.io
cp -R "$src/_script" .
cp "$src/Gemfile" "$src/.gitignore" "$src/.ruby-version" .
mkdir -p .bundle && cp "$src/.bundle/config" .bundle/config
```

- [ ] **Step 2: Write `_config.yml`**

Modelled on `kuamka.github.io/_config.yml`, with these values; drop `paginate`/`paginate_path`/`permalink`/`category_archive`/`tag_archive`/`author` defaults and the `jekyll-paginate`, `jekyll-gist`, `jemoji` plugins, since this site has no blog:

```yaml
title: "Snowmigos"
description: >-
  See where your crew is on the mountain
email: support@kuamka.com
url: https://snowmigos.com
name: "Kuamka Ltd"
locale: en-GB
github_username: kuamka
repository: kuamka/snowmigos-site
markdown: kramdown
remote_theme: mmistakes/minimal-mistakes
minimal_mistakes_skin: default
timezone: "Europe/London"
include:
  - _pages
  - .well-known
exclude:
  - docs
  - Gemfile
  - Gemfile.lock
  - node_modules
  - vendor/bundle/
  - vendor/cache/
  - vendor/gems/
  - vendor/ruby/
plugins:
  - jekyll-feed
  - jekyll-include-cache
  - jekyll-sitemap
defaults:
  - scope:
      path: ""
      type: pages
    values:
      layout: single
      author_profile: false
```

The `include:` entry for `.well-known` is what makes Jekyll copy a dot-directory into `_site`; without it the association files silently vanish. The `exclude:` list must be spelled out in full — Jekyll 3 replaces its defaults rather than adding to them, which is why `vendor/` is repeated.

- [ ] **Step 3: Write `CNAME`**

One line, no trailing newline issues:

```
snowmigos.com
```

- [ ] **Step 4: Install gems and build**

Run: `cd ~/Code/Kuamka/snowmigos-site && ./_script/setup && ./_script/build`
Expected: `bundle install` completes, then Jekyll reports `done in … seconds` and `_site/` exists. If `_script/setup` reports Ruby 3.0.0 missing, run `rbenv install 3.0.0` first.

- [ ] **Step 5: Commit and create the GitHub repo**

```bash
cd ~/Code/Kuamka/snowmigos-site
git init -b master
git add -A
git commit -m "chore: scaffold the Snowmigos site on the kuamka.com Jekyll stack

Co-Authored-By: Claude Opus 5 <noreply@anthropic.com>"
gh repo create kuamka/snowmigos-site --private --source=. --remote=origin --push \
  --description "Marketing site, privacy policy and app-association files for Snowmigos"
```

Keep it private for now; Task 7 makes it public, which GitHub Pages requires on a free plan. If the org is on a paid plan Pages works while private, but public is simpler and the content is public anyway.

- [ ] **Step 6: Verify the push**

Run: `gh repo view kuamka/snowmigos-site --json defaultBranchRef,visibility`
Expected: `"name": "master"`.

---

## Task 2: Vendor the app icon

**Files:**
- Create: `assets/images/snowmigos-icon.svg`
- Reference: `~/Code/Kuamka/piste-pals/assets/icon-traced.svg`, `~/Code/Kuamka/kuamka.github.io/assets/images/piste-pals-icon.svg`

**Interfaces:**
- Consumes: Task 1's repo.
- Produces: `/assets/images/snowmigos-icon.svg`, referenced by Tasks 3 and 5.

- [ ] **Step 1: Pick the source and copy it**

`kuamka.github.io/assets/images/piste-pals-icon.svg` is a 13.7 KB VTracer trace of the app icon with no wordmark in it, so it carries no old branding. Copy that file to `assets/images/snowmigos-icon.svg`. Diff it against `piste-pals/assets/icon-traced.svg` first; if they differ, prefer the app repo's, which is the live icon source.

- [ ] **Step 2: Verify no old name is embedded**

Run: `grep -ic "piste" assets/images/snowmigos-icon.svg`
Expected: `0`.

- [ ] **Step 3: Commit**

```bash
git add assets/images/snowmigos-icon.svg
git commit -m "feat: add the app icon as snowmigos-icon.svg"
```

---

## Task 3: Placeholder landing page and 404

**Files:**
- Create: `_pages/home.md`, `_pages/404.md`
- Reference: `~/Code/Kuamka/kuamka.github.io/_pages/home.md` (layout `splash` usage), `_pages/404.md`

**Interfaces:**
- Consumes: Task 2's `/assets/images/snowmigos-icon.svg`.
- Produces: `/` and the 404 page.

- [ ] **Step 1: Write `_pages/home.md`**

Front matter `permalink: /`, `title: "Snowmigos"`, `layout: splash`, `hidden: true`. Body: the icon at 96×96 with `border-radius`, a one-line description of the app, a line saying it is coming to the App Store and Google Play, and a link to <https://kuamka.com> as the company site. No feature row and no store buttons — there are no listings to link to yet, and a dead button is worse than none. British spelling; no trailing full stop on the single-sentence lines.

- [ ] **Step 2: Write `_pages/404.md`**

Copy `kuamka.github.io/_pages/404.md`, changing the copy to point at `/` rather than the Kuamka site. Keep its `permalink: /404.html`.

- [ ] **Step 3: Build and verify both pages render**

```bash
./_script/build
test -f _site/index.html && test -f _site/404.html
grep -c "kuamka.com" _site/index.html          # expect >= 1
grep -ic "piste" _site/index.html              # expect 0
```

Expected: both files exist, the kuamka.com link is present, no old name.

- [ ] **Step 4: Commit**

```bash
git add _pages/home.md _pages/404.md
git commit -m "feat: add a placeholder landing page and a 404"
```

---

## Task 4: Privacy policy at `/privacy/`

**Files:**
- Create: `_pages/privacy.md`
- Reference (port from): `~/Code/Kuamka/kuamka.github.io/_pages/piste-pals-privacy.md` (235 lines, already includes the retention and self-service-delete wording merged in PR #1 follow-ups)

**Interfaces:**
- Consumes: Task 1's repo.
- Produces: `/privacy/` with a `#deleting-your-data` anchor.

- [ ] **Step 1: Port the file**

Copy the source file to `_pages/privacy.md` and change only these things — the policy's substance is already correct and must not be rewritten:

- `permalink: /piste-pals/privacy/` becomes `permalink: /privacy/`
- `title: "Piste Pals privacy policy"` becomes `title: "Snowmigos privacy policy"`
- every remaining occurrence of `Piste Pals` becomes `Snowmigos` (10 in the source, including `title:`)
- `_Last updated: 1 July 2026_` becomes `_Last updated: 24 October 2026_`
- the "What this policy covers" sentence reading "the wider kuamka.com website" stays as it is — it is still true and still the company site

Keep `toc: true`, `toc_label`, `toc_sticky`, `layout: single`, `author_profile: false`. Keep every `## ` heading unchanged, so `#deleting-your-data` and the other anchors survive.

- [ ] **Step 2: Build and assert the anchor and the rename**

```bash
./_script/build
grep -c 'id="deleting-your-data"' _site/privacy/index.html   # expect 1
grep -ic "piste pals" _site/privacy/index.html               # expect 0
grep -c "privacy@kuamka.com" _site/privacy/index.html        # expect >= 4
grep -c "07961814" _site/privacy/index.html                  # expect 1
```

Expected: anchor present, old name gone, contact address and company number intact.

- [ ] **Step 3: Diff against the source to prove nothing else changed**

Run:
```bash
diff <(sed -e 's/Piste Pals/Snowmigos/g' \
           -e 's|permalink: /piste-pals/privacy/|permalink: /privacy/|' \
           -e 's/_Last updated: 1 July 2026_/_Last updated: 24 October 2026_/' \
           ~/Code/Kuamka/kuamka.github.io/_pages/piste-pals-privacy.md) \
     _pages/privacy.md
```
Expected: no output. Any difference is an unintended edit to the policy text.

- [ ] **Step 4: Commit**

```bash
git add _pages/privacy.md
git commit -m "feat: port the privacy policy to /privacy/ as Snowmigos"
```

---

## Task 5: Invite landing page at `/join/`

**Files:**
- Create: `_pages/join.md`
- Reference (port from): `~/Code/Kuamka/kuamka.github.io/_pages/piste-pals-join.md`

**Interfaces:**
- Consumes: Task 2's icon.
- Produces: `/join/`, the target of `https://snowmigos.com/join/?token=…` and of the `/join` app-link paths in Task 6.

- [ ] **Step 1: Port the page**

Copy the source, changing: `permalink: /join/`; title to "Join the trip on Snowmigos"; alt text and image to `snowmigos-icon.svg`; the element ids from `pp-` to `sm-`; and the deep link in the script from `pistepals://join/` to `snowmigos://join/`. Keep `encodeURIComponent(token)`. Keep the two store buttons as `href="#"` with their labels, or drop them — if they stay, they must be visibly inert rather than look live; prefer dropping them until the listings exist, matching the landing page decision in Task 3.

- [ ] **Step 2: Build and assert the deep link and the guard**

```bash
./_script/build
grep -c "snowmigos://join/" _site/join/index.html     # expect 1
grep -c "encodeURIComponent" _site/join/index.html    # expect 1
grep -ic "pistepals\|piste pals" _site/join/index.html # expect 0
```

Expected: the new scheme is there, the encoder survived, the old name is gone.

- [ ] **Step 3: Test the no-token and bad-token cases in a browser**

Serve locally (`./_script/serve`) and in the built-in browser open, in turn:
- `http://127.0.0.1:4000/join/` — the open-in-app link must stay hidden
- `http://127.0.0.1:4000/join/?token=abc123` — the link must appear with `href="snowmigos://join/abc123"`
- `http://127.0.0.1:4000/join/?token=has+bad/chars` — the link must appear with the slash percent-encoded as `%2F`, not raw

Read the `href` with `read_page` or `javascript_tool` rather than eyeballing a screenshot.

- [ ] **Step 4: Commit**

```bash
git add _pages/join.md
git commit -m "feat: port the invite landing page to /join/"
```

---

## Task 6: App-association files

**Files:**
- Create: `.well-known/apple-app-site-association`, `.well-known/assetlinks.json`
- Reference: `~/Code/Kuamka/kuamka.github.io/.well-known/*`

**Interfaces:**
- Consumes: Task 1's `_config.yml` `include: .well-known`.
- Produces: `/.well-known/apple-app-site-association` and `/.well-known/assetlinks.json`, verified over the network in Task 9.

- [ ] **Step 1: Write `apple-app-site-association`**

No file extension. Content exactly:

```json
{
  "applinks": {
    "apps": [],
    "details": [
      {
        "appID": "9DG4MJ43HD.com.kuamka.snowmigos",
        "paths": ["/join/*", "/join"]
      }
    ]
  }
}
```

- [ ] **Step 2: Get the local debug signing fingerprint**

Run: `keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android | grep "SHA256:"`
Expected: a colon-separated SHA-256. If the debug keystore does not exist, use the fingerprint already in `kuamka.github.io/.well-known/assetlinks.json` (`F5:63:…:B7`) — it is equally provisional, since it belongs to the old package.

- [ ] **Step 3: Write `assetlinks.json`**

Same shape as the source file, with `package_name` `com.kuamka.snowmigos` and the Step 2 fingerprint as the single entry in `sha256_cert_fingerprints`. JSON takes no comments, so the provisional status goes in a sibling `.well-known/README.md` that `_config.yml` does not need to exclude (Jekyll copies it; harmless) — one paragraph saying the fingerprint is a placeholder, that the real one is the new Play app's App Signing SHA-256, and linking issue #264.

- [ ] **Step 4: Build and assert both files land in `_site` as valid JSON**

```bash
./_script/build
python3 -m json.tool < _site/.well-known/apple-app-site-association > /dev/null && echo AASA-ok
python3 -m json.tool < _site/.well-known/assetlinks.json > /dev/null && echo links-ok
grep -c "com.kuamka.snowmigos" _site/.well-known/apple-app-site-association  # expect 1
grep -c "com.kuamka.snowmigos" _site/.well-known/assetlinks.json             # expect 1
```

Expected: both parse, both name the new ID. A missing file here means the `include:` entry in `_config.yml` is wrong.

- [ ] **Step 5: Commit**

```bash
git add .well-known
git commit -m "feat: declare the snowmigos.com universal and app link associations"
```

---

## Task 7: GitHub Pages and organisation domain verification

Browser-driven on github.com with the user's sign-in; use Claude in Chrome, requesting credentials through the password manager if a sign-in is needed. Load the Chrome tools and `enter_verification_code` in one `ToolSearch` call before starting.

**Files:** none in the repo.

**Interfaces:**
- Consumes: Task 1's pushed repo and `CNAME`.
- Produces: a Pages deployment serving `snowmigos.com`, and a verified domain for the `kuamka` org. The TXT record this produces is added in Task 8.

- [ ] **Step 1: Make the repo public**

Run: `gh repo edit kuamka/snowmigos-site --visibility public --accept-visibility-change-consequences`
Expected: no error. Then `gh repo view kuamka/snowmigos-site --json visibility` reports `PUBLIC`.

- [ ] **Step 2: Start organisation domain verification**

In the browser: GitHub, organisation `kuamka`, Settings, Pages, "Add a domain", enter `snowmigos.com`. Record the TXT record name and value it shows — verbatim, including the `_github-pages-challenge-kuamka` prefix. Do not click Verify yet; the record does not exist until Task 8.

- [ ] **Step 3: Turn Pages on for the repo**

Repo Settings, Pages: source "Deploy from a branch", branch `master`, folder `/ (root)`. The `CNAME` file means the custom domain field should already read `snowmigos.com`; if it does not, set it. "Enforce HTTPS" will be greyed out until the certificate is issued — leave it for Task 9.

- [ ] **Step 4: Verify the build ran**

Run: `gh api repos/kuamka/snowmigos-site/pages/builds/latest --jq '.status, .error.message'`
Expected: `built` and `null`. A `null` status with an error about the domain is expected before DNS exists; re-check after Task 8.

---

## Task 8: Cloudflare DNS, domain verification record, and the `.app` redirect

Browser-driven on dash.cloudflare.com with the user's sign-in.

**Files:** none in the repo.

**Interfaces:**
- Consumes: Task 7's TXT challenge record.
- Produces: `snowmigos.com` resolving to GitHub Pages, and `snowmigos.app` redirecting to `https://snowmigos.com`.

- [ ] **Step 1: Confirm both zones are on Cloudflare nameservers**

Run: `dig +short NS snowmigos.com; dig +short NS snowmigos.app`
Expected: `*.ns.cloudflare.com` for both. If either is still at a registrar's nameservers, that zone's records cannot be set here — say so and stop that half of the task.

- [ ] **Step 2: Add the apex records for GitHub Pages**

In the Cloudflare DNS tab for `snowmigos.com`, four `A` records on `@` — `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153` — and four `AAAA` records on `@` — `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153`. **Set every one of them to DNS only (grey cloud), not Proxied.** A proxied record makes GitHub's certificate issuance fail, and Cloudflare's own certificate in front of Pages creates a second TLS hop that the Apple AASA fetcher has no reason to tolerate. Proxying can be turned on later, deliberately, once the GitHub certificate exists.

- [ ] **Step 3: Add `www` and the verification TXT**

A `CNAME` on `www` to `kuamka.github.io`, DNS only. A `TXT` record with the exact name and value from Task 7 Step 2.

- [ ] **Step 4: Verify DNS from the command line**

```bash
dig +short A snowmigos.com
dig +short CNAME www.snowmigos.com
dig +short TXT _github-pages-challenge-kuamka.snowmigos.com
```
Expected: the four GitHub IPs, `kuamka.github.io.`, and the challenge value. DNS may take a few minutes; re-run rather than assuming.

- [ ] **Step 5: Finish GitHub's domain verification**

Back in the org Pages settings, click Verify. Expected: `snowmigos.com` listed as verified.

- [ ] **Step 6: Redirect `snowmigos.app`**

`snowmigos.app` needs a record to attach a redirect to: add a **proxied** (orange cloud) `AAAA` record on `@` pointing at `100::` — the documented discard address for proxy-only hostnames — and the same on `www`. Then Rules, Redirect Rules, create a rule: when `hostname` equals `snowmigos.app` or ends with `.snowmigos.app`, static redirect to `https://snowmigos.com`, status 301, preserve query string. Cloudflare's universal certificate covers the proxied `.app` host, so https works without an origin.

- [ ] **Step 7: Verify the redirect on both schemes**

```bash
curl -sSI https://snowmigos.app | head -3
curl -sSI http://snowmigos.app | head -3
curl -sSI https://www.snowmigos.app | head -3
```
Expected: `301` and `location: https://snowmigos.com` for each. The plain-http case is the one worth checking — a browser would have upgraded it under HSTS and hidden a rule that only matched https.

---

## Task 9: End-to-end verification of the live site

**Files:** none.

**Interfaces:**
- Consumes: Tasks 1–8.
- Produces: evidence that the app can be built against this domain.

- [ ] **Step 1: Enforce HTTPS**

Once the certificate is issued (GitHub repo Settings, Pages shows it), tick "Enforce HTTPS". Then `gh api repos/kuamka/snowmigos-site/pages --jq '.https_enforced, .status'` should report `true` and `built`.

- [ ] **Step 2: Assert the association files are served directly, with no redirect**

```bash
curl -sS -o /dev/null -w '%{http_code} %{num_redirects} %{content_type}\n' \
  https://snowmigos.com/.well-known/apple-app-site-association
curl -sS -o /dev/null -w '%{http_code} %{num_redirects} %{content_type}\n' \
  https://snowmigos.com/.well-known/assetlinks.json
```
Expected: `200 0` for both. **A non-zero redirect count is a failure**, not a cosmetic detail — it is the single most common reason universal links silently do not work. Also record, for the record and without treating them as failures, what `http://` and `www.` return for the same two paths.

- [ ] **Step 3: Assert the file contents over the network**

```bash
curl -sS https://snowmigos.com/.well-known/apple-app-site-association | python3 -m json.tool
curl -sS https://snowmigos.com/.well-known/assetlinks.json | python3 -m json.tool
```
Expected: the Task 6 content, with `9DG4MJ43HD.com.kuamka.snowmigos` and `com.kuamka.snowmigos`.

- [ ] **Step 4: Assert the pages**

```bash
for p in / /privacy/ /join/ ; do
  curl -sS -o /dev/null -w "$p %{http_code}\n" "https://snowmigos.com$p"
done
curl -sS https://snowmigos.com/privacy/ | grep -c 'id="deleting-your-data"'
```
Expected: `200` for all three, anchor count `1`.

- [ ] **Step 5: Run the two official validators in the browser**

- Apple: paste `https://snowmigos.com` into an AASA validator (branch.io/resources/aasa-validator, or Apple's app-site-association CDN URL `https://app-site-association.cdn-apple.com/a/v1/snowmigos.com`). The `appID` must resolve for `/join`.
- Google: the Digital Asset Links Statement List Tester with `https://snowmigos.com` and `com.kuamka.snowmigos`. It will report the statement as valid *for the placeholder fingerprint* — which is the expected state until #264 Phase 1 supplies the Play one. Note that in the result rather than treating it as a pass for the shipping app.

- [ ] **Step 6: Report the results**

Record each command's actual output. No step of this task is complete on the basis of "should be fine".

---

## Task 10: Strip Piste Pals from kuamka.com

A PR against `kuamka/kuamka.github.io`, branch from `master`.

**Files:**
- Delete: `_pages/piste-pals-privacy.md`, `_pages/piste-pals-join.md`, `assets/images/piste-pals-icon.svg`
- Modify: `.well-known/apple-app-site-association`, `.well-known/assetlinks.json`
- Search and fix: any nav, `_pages/products.md`, `_pages/home.md` or post copy mentioning the app

**Interfaces:**
- Consumes: Task 9's verified site — do not merge this until snowmigos.com is live, so no privacy URL is ever unreachable.
- Produces: a kuamka.com with no Piste Pals content.

- [ ] **Step 1: Branch from master in a worktree**

```bash
cd ~/Code/Kuamka/kuamka.github.io
git fetch origin
git worktree add ~/Code/Kuamka/kuamka-site-depiste -b chore/remove-piste-pals origin/master
cd ~/Code/Kuamka/kuamka-site-depiste
```

The main checkout is on `feat/products-page` with other work; a worktree keeps this clean.

- [ ] **Step 2: Find every mention before deleting anything**

Run: `git grep -in -E "piste[ _.-]?pals?|pistepals" -- . ':!_site' ':!vendor'`
Expected: a list. It should name the three files to delete plus the two `.well-known` files. Anything else it finds — a products tile, a nav entry, a blog post — must be handled in Step 4.

- [ ] **Step 3: Delete the pages and the icon**

```bash
git rm _pages/piste-pals-privacy.md _pages/piste-pals-join.md assets/images/piste-pals-icon.svg
```

- [ ] **Step 4: Empty the association files rather than deleting them**

`apple-app-site-association` becomes `{"applinks": {"apps": [], "details": []}}` and `assetlinks.json` becomes `[]`. Both files stay, so kuamka.com keeps answering those paths with valid empty statements instead of a 404 — the cleaner answer for anything that still asks. Then fix anything else Step 2 turned up: if a products tile or nav entry mentioned the app, point it at `https://snowmigos.com` rather than removing the mention entirely.

- [ ] **Step 5: Build and assert the old name is gone**

```bash
./_script/build
git grep -in -E "piste[ _.-]?pals?|pistepals" -- . ':!_site' ':!vendor' ; echo "exit=$?"
test ! -e _site/piste-pals
python3 -m json.tool < _site/.well-known/assetlinks.json
```
Expected: the grep prints nothing and reports `exit=1`, the old URL path is gone from the build, the JSON parses.

- [ ] **Step 6: Commit and open the PR**

```bash
git add -A
git commit -m "chore: remove the Piste Pals pages now the app lives at snowmigos.com

Co-Authored-By: Claude Opus 5 <noreply@anthropic.com>"
git push -u origin chore/remove-piste-pals
gh pr create --repo kuamka/kuamka.github.io --base master \
  --title "chore: remove the Piste Pals pages" --body "..."
```

PR body: what moved and where, that the association files are left as valid empty statements rather than 404s, and that old beta builds pointing at `applinks:kuamka.com` are knowingly broken because that beta was small. End with the attribution line:

```
🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

- [ ] **Step 7: Clean up the worktree after merge**

```bash
cd ~/Code/Kuamka/kuamka.github.io
git worktree remove ~/Code/Kuamka/kuamka-site-depiste
```

---

## Task 11: Close the loop on issue #264

**Files:** none — GitHub only.

**Interfaces:**
- Consumes: Tasks 1–10.
- Produces: #264's Phase 3 reflecting what is actually done, and an unmissable link between the placeholder fingerprint and the Phase 1 item that replaces it.

- [ ] **Step 1: Tick the Phase 3 boxes that are now done**

Edit the issue body: every item under "New site repo `kuamka/snowmigos-site`" and "DNS and hosting", plus Phase 5 step 1 and step 2. Leave the kuamka.com clean-up items for when Task 10's PR merges.

- [ ] **Step 2: Cross-link the fingerprint swap**

In the Phase 3 `assetlinks.json` line, name the placeholder explicitly and link the Phase 1 item. In the Phase 1 line "Get the new app's Play App Signing SHA-256 fingerprint", add that `kuamka/snowmigos-site/.well-known/assetlinks.json` and its `README.md` are what need editing when it arrives.

- [ ] **Step 3: Comment with the verification output**

Post Task 9's actual command output as a comment, so the next person does not have to re-derive whether the association files were ever served correctly.

- [ ] **Step 4: Verify**

Run: `gh issue view 264 --repo kuamka/piste-pals | grep -c "snowmigos-site"`
Expected: at least 2 (the repo name in Phase 3 and the new Phase 1 cross-reference).
