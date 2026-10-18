# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page, interactive Star Wars canon watch-order guide. It is plain static files with no build step, dependencies, package manager, tests or linter:

- `index.html`: everything in one file (inline `<style>`, markup, and one inline IIFE `<script>` in ES5 style with `var` and `function`).
- `posters/<p>.jpg`: one poster per franchise/title, referenced by each card's `p` field.
- `privacidad.html`: privacy policy. The Google OAuth consent screen points to it.
- `CHANGELOG.md`: release notes.

To run it, open `index.html` in a browser (works from `file://`). To serve the folder, use the `guia` entry in `.claude/launch.json` (`python -m http.server 8787`). Drive sync needs a real `localhost` origin, not `file://`. User-facing text, the changelog and commit messages are written in **Spanish**.

## Architecture (inside `index.html`'s script)

- **`ERAS`** is the single source of truth: an ordered list of eras (timeline stops), each with `cards`. The array order *is* the in-universe chronological watch order. Card fields:
  - `id` is a stable key. It is used for saved progress, so treat it as permanent.
  - `p` is the poster basename in `posters/`.
  - `t` is the title and `m` the meta line.
  - `k` is `"film"` or `"tv"` (drives the filters).
  - `rel` is the real release date (ISO).
  - `ep` (optional) lists episode names; when present, the card shows an expandable episode list.
- **Release-order view**: `RELEASE_BUCKETS` regroups the same cards by real release era. Each bucket has a `match(c)` predicate that checks ids or id prefixes (`cw-`, `toj`), and the first match wins. Inside a bucket, cards sort by `rel`. The last bucket is a catch-all, so a new card lands there unless an earlier bucket's `match` is updated.
- **Numbering** (01, 02, …) is computed while rendering (`renderSections`) and is continuous across sections. It therefore differs between the chronological and release views. Never hardcode it.
- **State**: `localStorage` key `sw-canon-explode-v1` stores `{ <cardId>: true, __schema101: true, __t: { <cardId>: ISO } }`. `__t` records when each card was last marked or unmarked. Any code that changes progress must update it: call `stamp()`, or `stampAll()` for reset and import.
- **Backup format**: `buildBackup()` produces `{ app, appVersion, version: 2, updated, watched: [ids], marks: { id: { w, t } } }`. Both the downloaded JSON and the Drive file use it. `watched` keeps older versions working. `mergeBackup()` merges per card: the newer `t` wins, and when neither side has a timestamp the result is the union. Loading a file *replaces* progress; Drive sync *merges* it.
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
- **Lightsaber progress bar**: `refresh()` sets the `--saber` CSS variable by interpolating HSL across `SABER_STOPS`. `refresh(true)` plays the ignite animation on load.

## Releases

Versioning is SemVer, and `CHANGELOG.md` follows the Keep a Changelog format (in Spanish). A release updates:

1. `APP_VERSION` and `APP_UPDATED` in the script. These overwrite the footer's hardcoded `#verTag`/`#verDate` at runtime; keep those in sync anyway.
2. A new `CHANGELOG.md` section, including its link reference at the bottom.
3. A commit titled `Release vX.Y.Z — <resumen>`.

Ordering changes are checked against Lucasfilm's official guides linked in the footer. The footer's methodology note explains how overlapping/parallel entries are handled; keep it accurate when ordering logic changes.
