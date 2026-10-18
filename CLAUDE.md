# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page, interactive Star Wars canon watch-order guide. It is plain static files with no build step, dependencies, package manager, tests or linter:

- `index.html`: everything in one file (inline `<style>`, markup, and one inline IIFE `<script>` in ES5 style with `var` and `function`).
- `posters/<p>.webp`: one poster per franchise/title (500 px wide), referenced by each card's `p` field. The `.jpg` files are the originals and are no longer loaded.
- `icons/`: the clone trooper helmet. `icon.svg` is the source (app icon), `favicon.svg` is a tighter crop and `icon-maskable.svg` has more padding. The PNGs are rendered from them with `sharp` (global `sharp-cli`). `og-image.jpg` is the 1200×630 link preview used by the Open Graph tags.
- `sw.js`, `manifest.webmanifest`: installable PWA that works offline. `sw.js` serves pages network-first and posters/icons cache-first, and only handles same-origin requests.
- `privacidad.html`: privacy policy. The Google OAuth consent screen points to it.
- `CHANGELOG.md`: release notes.

To run it, open `index.html` in a browser (works from `file://`). To serve the folder, use the `guia` entry in `.claude/launch.json` (`python -m http.server 8788`). Drive sync needs a real `localhost` origin, not `file://`. User-facing text, the changelog and commit messages are written in **Spanish**.

## Architecture (inside `index.html`'s script)

- **`ERAS`** is the single source of truth: an ordered list of eras (timeline stops), each with `cards`. The array order *is* the in-universe chronological watch order. Card fields:
  - `id` is a stable key. It is used for saved progress, so treat it as permanent.
  - `p` is the poster basename in `posters/`.
  - `t` is the title and `m` the meta line.
  - `k` is `"film"` or `"tv"` (drives the filters).
  - `rel` is the real release date (ISO).
  - `ep` (optional) lists episode names; when present, the card shows an expandable list of episodes that can each be checked.
- **Release-order view**: `RELEASE_BUCKETS` regroups the same cards by real release era. Each bucket has a `match(c)` predicate that checks ids or id prefixes (`cw-`, `toj`), and the first match wins. Inside a bucket, cards sort by `rel`. The last bucket is a catch-all, so a new card lands there unless an earlier bucket's `match` is updated.
- **Numbering** (01, 02, …) is computed while rendering (`renderSections`) and is continuous across sections. It therefore differs between the chronological and release views. Never hardcode it.
- **State**: `localStorage` key `sw-canon-explode-v1` stores `{ <key>: true, __schema101: true, __t: { <key>: ISO } }`.
  - A key is a card id or an episode key `<cardId>/<n>` (`epKeys(c)`, n from 1). `KEYS` lists both kinds.
  - A watched card counts all its episodes as watched. Episode keys are partial progress on an unwatched card; checking the last one marks the card and clears them.
  - `__t` records when each key was last changed. Change progress only through `setKey()` (or `stampAll()` for reset/import), then call `changed()` (save, refresh, schedule Drive).
- **Backup format**: `buildBackup()` produces `{ app, appVersion, version: 2, updated, watched: [ids], marks: { id: { w, t } } }`. Both the downloaded JSON and the Drive file use it. `watched` keeps older versions working. `mergeBackup()` merges per key: the newer `t` wins, and when neither side has a timestamp the result is the union. Loading a file *replaces* progress; Drive sync *merges* it.
- **Google Drive sync** (the second half of the script) is a port of the Cotizador's `src/lib/drive.ts` (`C:\Proyectos\Chiguiro\Cotizador`):
  - No server. Google Identity Services gives a `drive.file` token that lasts one hour and is kept only in memory.
  - Connection state, without tokens, lives in `localStorage` under `sw-canon-explode-v1:drive`.
  - `driveUpload()` first pulls and merges the Drive file if it changed since this device last saw it, then uploads.
  - Uploads run `DRIVE_DELAY` after a change and when the page is hidden.
  - The feature shows only when `DRIVE_CLIENT_ID` is set and the hostname is in `DRIVE_HOSTS`.
  - Requests for the token popup must come from inside a click handler: `getToken(true)` runs synchronously at the start of the chain.
- **Progress migration**: when a card is split or renamed, add a rule to `expandLegacyProgress` (old id → new ids) so existing progress carries over. It runs:
  - once on stored state, guarded by the `__schema101` flag;
  - on imported JSON whose `appVersion` is missing or `1.0.0`.

  A future migration may need a new flag or version check of the same shape.
- **UI refresh**: `refresh()` updates the saber, the per-era counts (`paintEras`) and the "Seguir viendo" target (`updateNext`). `applyFilter()` combines the Películas/Series filter with the search `query`. Both work on the cards currently visible, so they follow the active view.
- **Lightsaber progress bar**: `refresh()` sets the `--saber` CSS variable by interpolating HSL across `SABER_STOPS`. `refresh(true)` plays the ignite animation on load.

## Releases

Versioning is SemVer, and `CHANGELOG.md` follows the Keep a Changelog format (in Spanish). A release updates:

1. `APP_VERSION` and `APP_UPDATED` in the script, and `CACHE` in `sw.js` (so installed copies drop the old cache). `APP_VERSION`/`APP_UPDATED` overwrite the footer's hardcoded `#verTag`/`#verDate` at runtime; keep those in sync anyway.
2. A new `CHANGELOG.md` section, including its link reference at the bottom.
3. A commit titled `Release vX.Y.Z — <resumen>` and an annotated tag `vX.Y.Z`.

Ordering changes are checked against Lucasfilm's official guides linked in the footer. The footer's methodology note explains how overlapping/parallel entries are handled; keep it accurate when ordering logic changes.
