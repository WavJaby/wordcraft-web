# WordCraft: unofficial browser experience

Try https://wavjaby.github.io/wordcraft-web/. Original project: https://github.com/storytold/wordcraft. WordCraft is made by the ArtCraft Team and contributors; this mirror is not operated or endorsed by them.

## Official CI preview

The official JS/Wasm inputs come from a CI preview, not a published upstream Release: version 0.1.0, source commit `c9824abfc20619ccc9dd876cfa1cc9ceb7c4dca4`, successful [workflow 37600854436](https://github.com/storytold/wordcraft/actions/runs/37600854436), artifact `release-web` 11473520511. GitHub's recorded artifact digest matches the downloaded archive. `upstream-files.json` pins both archives and every original application file. Upstream CI artifacts can expire; reproducible deployment requires the unchanged inner ZIP to be preserved as the pinned GitHub Release asset. No Wasm or ZIP is tracked in Git.

The supplied Gitea URL mirrors the original GitHub project. No public hosted editor was found in the official README, official product page, 87 GitHub forks or targeted searches on 2026-10-08; unindexed sites cannot be ruled out. The official product page returned HTTP 404.

## Hosting

Run `node scripts/build-site.cjs` for a dry run; add `--write` to create `_site/`, or `--output DIR` for a fresh destination. The builder verifies original bytes, gzips Wasm, splits it into content-addressed 512 KiB parts and gates byte-exact reconstruction. Generated chunks exist only in the Pages artifact. Four bounded downloads feed streaming decompression; per-part and whole-file SHA-256 verification gates completion and caching. Concurrency is not a measured optimum.

The shell provides progress, original/build links, a collapsible toolbar, transparent credits and a dismissible notice. English is default; the first preferred browser language beginning with `zh` selects Traditional Chinese. Preferences persist when storage is allowed. Shell colours match `crates/ui-egui/src/theme.rs:Tokens::light`; changing the editor theme does not change the shell.

Below 900 CSS pixels, the editor fits a 960-pixel virtual viewport; 50%/75%/100% overrides persist. This adjusts display scale, not touch support. `?sample` opens the official sample document. `?webgl` forces WebGL2; default startup prefers WebGPU. A reported GPU startup error retries the unchanged application once with WebGL2. Explicit overrides, unrelated errors, slow startup and failures after editing starts never trigger an automatic reload. WebGL2 is a compatibility option, not a guaranteed speed improvement.

## Browser differences

Static/configuration evidence from the pinned CI source; no exhaustive feature-parity claim:

| Area | Browser behaviour | Source |
|---|---|---|
| Files | Async picker / drag-and-drop; Save / Export download new files without overwriting originals | `apps/wordcraft-web/src/web.rs:services`, `download`; `crates/ui-egui/src/control.rs` |
| Persistence | Web entry disables AutoSave; this mirror adds no document recovery. Download saves before closing / reloading | `apps/wordcraft-web/src/web.rs:start`; `crates/ui-egui/src/lib.rs:logic` |
| Rendering | WebGPU / WebGL2; layout and document raster work depend on CPU and browser memory | `apps/wordcraft-web/Cargo.toml`; `crates/ui-egui/src/canvas.rs` |
| Integration | No desktop paths or desktop TCP/MCP server | `apps/wordcraft-web/src/web.rs:services`; `apps/wordcraft-web/src/main.rs` |
| Print / fonts | Print workflow exports a PDF; bundled fonts determine glyph coverage rather than automatic access to installed fonts | `crates/ui-egui/src/backstage.rs`; `crates/ui-egui/src/theme.rs:install_fonts` |

Browser restrictions do not imply identical desktop restrictions. WordCraft remains in development; test your documents and keep backups.

## Licenses

WordCraft is MIT OR Apache-2.0. Preserve [NOTICE](app/NOTICE), [ATTRIBUTION.md](app/ATTRIBUTION.md), original licenses and font notices. Notices absent from the ZIP are supplemented from the exact CI source commit; default-font notices come from `epaint_default_fonts` 0.36.2. Independent host code adapted from the community mirrors is MIT. [ArtCraft brand terms](app/docs/brand/LICENSE-brand.txt) are separate; the host uses plain-text attribution and no extracted logos. No complete transitive dependency-license audit is claimed.

`node --test tests/*.test.cjs` runs unit/selftest gates for delivery, failures, integrity, caching and provenance. Browser startup, import/save and mobile checks are separate runtime evidence.

## Single-page hosting and updates

The community bootstrap in `app-loader.js` mounts the official canvas in the main document; no iframe is created. Original JS/Wasm are verified against `upstream-files.json`. The host, loader and layout are community code, not an official build or endorsement. Keep upstream copyright, licenses, NOTICE and third-party attributions; do not extract ArtCraft brand marks into the host.

Wasm and compressed parts are ignored. CI downloads the exact archive pinned by URL + SHA-256, restores the verified Wasm, generates a fresh Pages artifact, and deploys it without committing binaries. WordCraft previews must be archived as GitHub Release assets because upstream CI artifacts expire. Browser asset caches retire prior Wasm hashes on service-worker activation. Existing Git history is not rewritten by this change.

1. Select an explicit official web ZIP and verify its published SHA-256; never silently follow latest.
2. Run `node scripts/update-upstream.cjs --archive HTTPS_ZIP_URL --sha256 SHA256 --version VERSION` to inspect the update, then repeat with `--write`. The command rejects changed archive/bootstrap contracts; review upstream licensing, supplemental notices and web/desktop differences.
3. Run `node scripts/build-site.cjs --output _site-review --write` and `node --test tests/*.test.cjs`; perform fresh-browser import/edit/export, mobile and renderer-failure checks.
4. Commit only source, configuration and provenance metadata, then push. Pages builds its current version from scratch; neither the old nor the new Wasm/parts enter Git.

For a clean checkout, `node scripts/update-upstream.cjs --restore` restores only the pinned Wasm. `--restore --archive LOCAL_ZIP` accepts a local copy with the same pinned archive hash.
