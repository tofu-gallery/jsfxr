# jsfxr on Tofu

jsfxr is a browser-based generator for retro game sound effects, ported from DrPetter's sfxr by Eric Fredricksen and maintained by Chris McCormick. Generate a sound from a preset, adjust its synthesis parameters, download a WAV file, save or reopen its JSON settings, or share the sound through a URL fragment. Sound synthesis and file processing happen in the visitor's browser.

## Run on Tofu

- Framework preset: `static`.
- Site root and output directory: repository root (`.`).
- Entry page: `index.html`.
- Build command: none. The committed HTML, JavaScript, logo, and favicon are the complete application.
- Runtime dependency installation: none. This revision has no `package.json`; the page loads `riffwave.js` and `sfxr.js` directly.
- Environment variables: none.
- Database: none.
- Accounts, API keys, paid services: none.

Serve the repository root over HTTP or HTTPS. For example:

```sh
python3 -m http.server 48763 --bind 127.0.0.1 --directory /path/to/jsfxr
```

Tofu serves the committed static files directly. Import the pinned `tofu` revision, not the unadapted default branch: the default branch carries npm and test configuration that Tofu's Free plan does not accept in a finished static site.

## Adaptations

Tofu's Free plan runs a finished site on its static host only when every file is a type that host serves and nothing in the tree is build or server configuration; one extensionless file or one `package.json` anywhere in the tree is enough to refuse the whole site. Upstream's repository root has five files that fail that rule, so the `tofu` branch changes them. Nothing the page loads was changed except one link.

- `UNLICENSE` is renamed to `UNLICENSE.txt`, text unchanged. The static host serves `.txt` files but not a file with no extension, and the licence must stay with the code. The licence link in the page footer (the `¢` before "2011") pointed at `UNLICENSE`, so its `href` now points at `UNLICENSE.txt`; that single attribute is the only edit to an upstream file.
- `sfxr-to-wav` is removed. It is upstream's Node command-line script for turning a sound definition into a WAV file, published through the npm package; the page never loads it, and a file with no extension is not served. The tip at the bottom of the page that mentions the `sfxr-to-wav` script describes that npm command and is left as upstream wrote it.
- `package.json` and `package-lock.json` are removed. The manifest holds upstream's npm publishing settings, its `test`, `test:e2e` and `serve` scripts and three development tools (`@playwright/test`, `live-server`, `tape`), and Tofu reads a `package.json` as the sign of a project that must be built. The lockfile had been added by this fork's first adaptation commit to make `npm ci` reproducible; it goes with the manifest.
- `playwright.config.js` is removed. It configures upstream's browser tests, and Tofu reads a `*.config.*` file as build configuration.
- `TOFU.md` records the static hosting settings, these changes, provenance, and validation of this gallery version.

Everything else is as upstream published it, including the application name, logo, favicon, copyright notices and licence text. The test files `tests.js` and `e2e-tests.js` stay unchanged, but they need the manifest and Playwright configuration that only the default branch has (see below). The fork's `main` branch mirrors the upstream `master` commit, and `tofu` holds these changes. No tracking, advertising, analytics, service credential, or Tofu account is added. Runtime scripts and images are local files; the existing upstream documentation links and social metadata remain as published by upstream.

## How upstream builds and tests it

There is no build step. `index.html` loads `riffwave.js` and `sfxr.js` as committed. Upstream's `package.json` describes the npm package (`sfxr.js`/`sfxr.mjs`, `riffwave.js`/`riffwave.mjs`, `UNLICENSE`, `README.md` and the `sfxr-to-wav` command) and carries the test tooling. To run upstream's own tests, use this fork's `main` branch, which is upstream's `master` and still has that manifest and the Playwright configuration:

```sh
git clone --branch main https://github.com/tofu-gallery/jsfxr.git jsfxr-upstream
cd jsfxr-upstream
npm install
npm test          # tests.js (tape): slider to parameter conversion round trips
npm run test:e2e  # e2e-tests.js (Playwright, Chromium) against live-server on port 8000
npm run serve     # live-server on the repository root, a development convenience
```

Playwright needs its Chromium download (`npx playwright install chromium`) before `npm run test:e2e` can start.

## Verification

Verified locally on 2026-10-23 with Node.js 22.22.3, npm 10.9.8, and Python 3.11.4. No app was deployed.

The unit suite and the first browser flow below ran on the previous pin, `pin/2026-10-23` (revision `d6b0643`), which still had the npm manifests. The files they exercise (`sfxr.js`, `riffwave.js`, `tests.js`) are unchanged in this revision.

Dependency preparation and the upstream unit suite:

```sh
npm --prefix /private/tmp/gallery-batch2/jsfxr install --package-lock-only --ignore-scripts --no-audit --no-fund
npm --prefix /private/tmp/gallery-batch2/jsfxr ci --ignore-scripts --no-audit --no-fund
npm --prefix /private/tmp/gallery-batch2/jsfxr test
```

Installation completed successfully. The unit suite passed all 3,100 assertions. There is no build script or required build step. npm reported deprecation warnings for upstream development-tool dependencies; those packages are not used by the deployed page.

Static serving and entry-page verification:

```sh
python3 -m http.server 48763 --bind 127.0.0.1 --directory /private/tmp/gallery-batch2/jsfxr
curl --fail --silent --show-error --output /dev/null --write-out 'GET / HTTP %{http_code}\n' http://127.0.0.1:48763/
```

The entry page returned HTTP 200. The same curl check returned HTTP 200 for `/sfxr.js`, `/riffwave.js`, `/logo.svg`, and `/icon.png`.

A local real-browser flow through the gstack `/browse` skill and Aside checked all 11 generator presets, nonempty WAV data and download links, 8,000 Hz / 16-bit WAV headers, JSON serialization and deserialization, and URL-fragment round trips. All passed. Console errors were `[]` and external resource requests were `[]`. Physical audio output was not independently listened to.

The complete upstream Playwright suite was not run: its sample-rate list still expects a 5,512 Hz control, while the current page offers 8,000 Hz. The browser flow explicitly checked the current 8,000 Hz control instead. The upstream test files remain unchanged.

## Provenance and licence

- Upstream: https://github.com/chr15m/jsfxr
- Upstream branch: `master`.
- Base upstream commit: `b7b6aa27d62f8f356db267276bf54b451555c49d`.
- Gallery fork: https://github.com/tofu-gallery/jsfxr
- Adaptation branch: `tofu`.
- Gallery pins: `pin/2026-10-23` (first adaptation, with the npm and Playwright files) and `pin/2026-10-23.1` (this revision, without them).
- Licence: The Unlicense. See `UNLICENSE.txt`, the upstream `UNLICENSE` file renamed with its text unchanged.

The app is browser-only: it has no cloud library or cross-device account. Keep sounds by downloading their WAV/JSON files or retaining their permalink. Browser autoplay policy may require clicking a generator or Play before sound can be heard.
