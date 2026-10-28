# Hush House site

Static support and privacy pages for the iOS game **Hush House** (Turkish: **Huzur Apartmanı**), for hosting on GitHub Pages. No build step, no JavaScript, no trackers.

| File | Purpose |
|---|---|
| `index.html` | Support page, English (`#en`) and Turkish (`#tr`): about the game, contact, FAQ |
| `privacy.html` | Privacy policy, English (`#en`) and Turkish (`#tr`) |
| `app-ads.txt` | AdMob app-ads.txt template |
| `.nojekyll` | Tells GitHub Pages to serve the files as they are, without Jekyll |

## 1. Fill the placeholders before publishing

| Placeholder | Where | Replace with |
|---|---|---|
| `{{SUPPORT_EMAIL}}` | `index.html` (4 times), `privacy.html` (16 times) | The real support email address |
| `pub-XXXXXXXXXXXXXXXX` | `app-ads.txt` | Your AdMob publisher ID (AdMob › Settings › Account information) |

Replace all at once, then check that nothing is left:

```sh
sed -i '' 's/{{SUPPORT_EMAIL}}/YOUR_REAL_ADDRESS/g' index.html privacy.html   # put the real address here
sed -i '' 's/pub-XXXXXXXXXXXXXXXX/pub-YOUR_16_DIGITS/' app-ads.txt              # put the real ID here
grep -rn 'SUPPORT_EMAIL\|XXXXXXXX' . --exclude=README.md --exclude-dir=.git      # must print nothing
```

## 2. Pick the repository name to match the app's privacy link

The app currently opens this privacy link from the Premium screen (`App/Sources/Views/PremiumSheet.swift`, `Links.privacy`):

```
https://tolgasayan.github.io/huzur-apartmani/privacy
```

That URL only works if the GitHub account is `tolgasayan` and the repository is named **`huzur-apartmani`**. Either publish this folder under that name, or change `Links.privacy` in the app (and the App Store Connect privacy URL) to the real address before submitting. GitHub Pages serves `privacy.html` at `/privacy` too.

## 3. Publish on GitHub Pages

```sh
gh repo create huzur-apartmani --public --source=. --remote=origin --push
gh api -X POST repos/tolgasayan/huzur-apartmani/pages -f 'source[branch]=main' -f 'source[path]=/'
```

Or in the browser: create a public repo, push this folder, then **Settings › Pages › Build and deployment › Deploy from a branch › `main` / `(root)`**. After a minute or two the site is at `https://tolgasayan.github.io/huzur-apartmani/`.

Then in App Store Connect set:

- **Privacy Policy URL:** `https://tolgasayan.github.io/huzur-apartmani/privacy.html` (or `/privacy`)
- **Support URL:** `https://tolgasayan.github.io/huzur-apartmani/`

## 4. app-ads.txt must be at the domain root

AdMob looks for `app-ads.txt` at the **root of the domain** of the developer website listed in App Store Connect, for example `https://tolgasayan.github.io/app-ads.txt`. A project site like `tolgasayan.github.io/huzur-apartmani/` can't serve that path. Options:

- **User site:** create a repo named `tolgasayan.github.io`, put `app-ads.txt` in its root, and use `https://tolgasayan.github.io` as the Marketing/developer website in App Store Connect.
- **Custom domain:** point a domain at GitHub Pages (Settings › Pages › Custom domain) so this repo is served at the root, and use that domain as the developer website.

Leaving `app-ads.txt` in this repo is harmless; it just won't be the copy AdMob verifies unless the site is served at a domain root. After publishing, AdMob › Apps › app-ads.txt shows whether it was found (it can take up to 24 hours).

## 5. Keep the policy in sync with the app

The privacy policy describes what the code does as of 28 October 2026: Google AdMob behind Google UMP consent, the Meta SDK (auto-logging off; app open plus tutorial / floor 10 / floor 30 / subscribe events), the ATT prompt for the IDFA, local notifications, StoreKit, and on-device storage only. If an SDK, event or data flow changes, update both language sections and the effective date.
