# App-association files

`apple-app-site-association` and `assetlinks.json` tell iOS and Android that
this site speaks for the Snowmigos app, which is what makes
`https://snowmigos.com/join/?token=…` open the app instead of the browser.

Both are served from the apex over https with no redirect. Apple's CDN and
Google's statement-list fetcher do not follow redirects, so a redirect here
breaks app links silently — see the checks in the site plan.

**The SHA-256 in `assetlinks.json` is a placeholder.** It is the old
`com.kuamka.pistepals` signing fingerprint, kept only so the file has the
right shape. Android links will not verify until it is replaced with the
Play App Signing SHA-256 of the new `com.kuamka.snowmigos` app, which is a
Phase 1 item in <https://github.com/kuamka/piste-pals/issues/264>. Replacing
it is a one-line edit to `assetlinks.json` and a delete of this paragraph.
