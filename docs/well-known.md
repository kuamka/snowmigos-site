# App-association files

`.well-known/apple-app-site-association` and `.well-known/assetlinks.json` tell
iOS and Android that this site speaks for the Snowmigos app, which is what makes
`https://snowmigos.com/join/?token=…` open the app instead of the browser.

Both are served from the apex over https with no redirect. Apple's CDN and
Google's statement-list fetcher do not follow redirects, so a redirect there
breaks app links silently. Check it with:

```bash
curl -sS -o /dev/null -w '%{http_code} redirects=%{num_redirects}\n' \
  https://snowmigos.com/.well-known/apple-app-site-association
curl -sS -o /dev/null -w '%{http_code} redirects=%{num_redirects}\n' \
  https://snowmigos.com/.well-known/assetlinks.json
```

`redirects=0` is the part that matters.

This file lives in `docs/`, which `_config.yml` excludes from the build. It must
not move back into `.well-known/`: `include: .well-known` makes Jekyll process
everything in there, so a note placed beside the association files is published
as a page and listed in `sitemap.xml`.

## assetlinks.json is empty on purpose

`assetlinks.json` is an empty list, so **Android App Links do not verify yet** and
invite links open in the browser on Android. Filling it in needs the Play App
Signing SHA-256 of the new `com.kuamka.snowmigos` app, which does not exist until
the Play listing does — a Phase 1 item in
<https://github.com/kuamka/piste-pals/issues/264>.

An empty list is deliberate rather than a placeholder fingerprint: a wrong
fingerprint makes Google's statement-list tester report a *valid* statement, which
is the kind of output someone ticks a box on. Empty reports nothing, which is the
truth. When the real fingerprint arrives, the file becomes:

```json
[
  {
    "relation": ["delegate_permission/common.handle_all_urls"],
    "target": {
      "namespace": "android_app",
      "package_name": "com.kuamka.snowmigos",
      "sha256_cert_fingerprints": ["<Play App Signing SHA-256>"]
    }
  }
]
```
