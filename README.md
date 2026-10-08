# WordCraft: unofficial browser experience

Try https://wavjaby.github.io/wordcraft-web/. Original project: https://github.com/storytold/wordcraft. WordCraft is made by the ArtCraft Team and contributors; this mirror is not operated or endorsed by them.

## Official release

Official JS/Wasm come from [WordCraft v0.3.0](https://github.com/storytold/wordcraft/releases/tag/v0.3.0). The archive matches the release's `SHA256SUMS.txt`; `upstream-files.json` pins its public URL, SHA-256 and every original application file. No Wasm or ZIP is tracked in Git.


## Hosting

Run `node scripts/build-site.cjs` for a dry run; add `--write` to create `_site/`, or `--output DIR` for a fresh destination. The builder verifies original bytes, gzips Wasm, splits it into content-addressed 512 KiB parts and gates byte-exact reconstruction. Generated chunks exist only in the Pages artifact. Four bounded downloads feed streaming decompression; per-part and whole-file SHA-256 verification gates completion and caching. Concurrency is not a measured optimum.

The shell provides progress, original/build links, a collapsible toolbar, transparent credits and a dismissible notice. English is default; the first preferred browser language beginning with `zh` selects Traditional Chinese. Preferences persist when storage is allowed. Shell colours match `crates/ui-egui/src/theme.rs:Tokens::light`; changing the editor theme does not change the shell.

Below 900 CSS pixels, the editor fits a 960-pixel virtual viewport; 50%/75%/100% overrides persist. This adjusts display scale, not touch support. `?sample` opens the official sample document. `?webgl` forces WebGL2; default startup prefers WebGPU. A reported GPU startup error retries the unchanged application once with WebGL2. Explicit overrides, unrelated errors, slow startup and failures after editing starts never trigger an automatic reload. WebGL2 is a compatibility option, not a guaranteed speed improvement.

## Browser differences

Static/configuration evidence from the v0.3.0 source; no exhaustive feature-parity claim:

| Area | Browser behaviour | Source |
|---|---|---|
| Files | Async picker / drag-and-drop; Save / Export download new files without overwriting originals | `apps/wordcraft-web/src/web.rs:services`, `download`; `crates/ui-egui/src/control.rs` |
| Persistence | Web entry disables AutoSave; this mirror adds no document recovery. Download saves before closing / reloading | `apps/wordcraft-web/src/web.rs:start`; `crates/ui-egui/src/lib.rs:logic` |
| Rendering | WebGPU / WebGL2; layout and document raster work depend on CPU and browser memory | `apps/wordcraft-web/Cargo.toml`; `crates/ui-egui/src/canvas.rs` |
| Integration | No desktop paths or desktop TCP/MCP server | `apps/wordcraft-web/src/web.rs:services`; `apps/wordcraft-web/src/main.rs` |
| Print / fonts | Print workflow exports a PDF; bundled fonts determine glyph coverage rather than automatic access to installed fonts | `crates/ui-egui/src/backstage.rs`; `crates/ui-egui/src/theme.rs:install_fonts` |

Browser restrictions do not imply identical desktop restrictions. WordCraft remains in development; test your documents and keep backups.

## Licenses

WordCraft is MIT OR Apache-2.0. Preserve [NOTICE](app/NOTICE), [ATTRIBUTION.md](app/ATTRIBUTION.md), original licenses and font notices. Notices absent from the ZIP are supplemented from the release tag; default-font notices come from `epaint_default_fonts` 0.36.2. Independent host code adapted from the community mirrors is MIT. [ArtCraft brand terms](app/docs/brand/LICENSE-brand.txt) are separate; the host uses plain-text attribution and no extracted logos. No complete transitive dependency-license audit is claimed.

`node --test tests/*.test.cjs` runs unit/selftest gates for delivery, failures, integrity, caching and provenance. Browser startup, import/save and mobile checks are separate runtime evidence.

## Single-page hosting and updates

The community bootstrap in `app-loader.js` mounts the official canvas in the main document; no iframe is created. Original JS/Wasm are verified against `upstream-files.json`. The host, loader and layout are community code, not an official build or endorsement. Keep upstream copyright, licenses, NOTICE and third-party attributions; do not extract ArtCraft brand marks into the host.

Wasm and compressed parts are ignored. CI downloads the exact archive pinned by URL + SHA-256, restores the verified Wasm, generates a fresh Pages artifact, and deploys it without committing binaries. Browser asset caches retire prior Wasm hashes on service-worker activation. Existing Git history is not rewritten by this change.

1. Select an explicit official web ZIP and verify its published SHA-256; never silently follow latest.
2. Run `node scripts/update-upstream.cjs --archive HTTPS_ZIP_URL --sha256 SHA256 --version VERSION` to inspect the update, then repeat with `--write`. The command rejects changed archive/bootstrap contracts; review upstream licensing, supplemental notices and web/desktop differences.
3. Run `node scripts/build-site.cjs --output _site-review --write`, then set `ARTCRAFT_SITE_DIR=_site-review` and run `node --test tests/*.test.cjs`; perform fresh-browser import/edit/export, mobile and renderer-failure checks.
4. Commit only source, configuration and provenance metadata, then push. Pages builds its current version from scratch; neither the old nor the new Wasm/parts enter Git.

For a clean checkout, `node scripts/update-upstream.cjs --restore` restores only the pinned Wasm. `--restore --archive LOCAL_ZIP` accepts a local copy with the same pinned archive hash.
