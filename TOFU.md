# jsfxr on Tofu

jsfxr is a browser-based generator for retro game sound effects, ported from DrPetter's sfxr by Eric Fredricksen and maintained by Chris McCormick. Generate a sound from a preset, adjust its synthesis parameters, download a WAV file, save or reopen its JSON settings, or share the sound through a URL fragment. Sound synthesis and file processing happen in the visitor's browser.

## Run on Tofu

- Framework preset: `static`.
- Site root and output directory: repository root (`.`).
- Entry page: `index.html`.
- Build command: none. The committed HTML, JavaScript, logo, and favicon are the complete application.
- Runtime dependency installation: none. The npm packages are development/test tools only.
- Environment variables: none.
- Database: none.
- Accounts, API keys, paid services: none.

Serve the repository root over HTTP or HTTPS. For example:

```sh
python3 -m http.server 48763 --bind 127.0.0.1 --directory /path/to/jsfxr
```

Do not use `npm run serve` as a production server; it is an upstream development convenience. Tofu serves the committed static files directly. Import the pinned `tofu` revision, not the unadapted default branch.

## Adaptations

Only these files were added:

- `package-lock.json`: pins the upstream development dependencies so validation can use `npm ci` reproducibly. No runtime dependency or build step was introduced.
- `TOFU.md`: records the static hosting settings, provenance, and validation of this gallery version.

All existing upstream files remain unchanged, including the application name, logo, favicon, copyright notices, and `UNLICENSE`. The fork's `main` branch mirrors the upstream `master` commit, and `tofu` contains these additions. No tracking, advertising, analytics, service credential, or Tofu account is added. Runtime scripts and images are local files; the existing upstream documentation links and social metadata remain as published by upstream.

## Verification

Verified locally on 2026-10-23 with Node.js 22.22.3, npm 10.9.8, and Python 3.11.4. No app was deployed.

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
- Gallery pin: `pin/2026-10-23`.
- Licence: The Unlicense. See the unchanged `UNLICENSE` file.

The app is browser-only: it has no cloud library or cross-device account. Keep sounds by downloading their WAV/JSON files or retaining their permalink. Browser autoplay policy may require clicking a generator or Play before sound can be heard.
