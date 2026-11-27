# PEF 19 schedule

A single static page (`index.html`) for the Panorama of the European Film 19 (Cairo, 26 Nov – 8 Dec 2026).

## Main rule: never break existing storage

The site is live and people already have their picks saved in their browsers. Every change must keep their saved data working. If a change can't do that, stop and ask first.

### What is stored (localStorage)

| Key | Format | Notes |
|---|---|---|
| `pef19-picks2` | `{ "<screeningId>": "b" \| "u" }` | Main picks. `b` = booked, `u` = not booked yet. |
| `pef19-picks` | `["<screeningId>", ...]` | Old format. Still read as a fallback when `pef19-picks2` is missing. Keep that fallback. |
| `pef19-ui` | `{ venue, arsubs, sort, travel }` | UI settings. |
| `pef19-hide` | `{ ended, chosen, s: {screeningId:1}, f: {filmId:1} }` | Hide toggles and hidden screenings/films. Added 27 Nov 2026. |
| `pef19-test` | `"1"` | Temporary write test; removed right away. |

Backup codes (`PEF19:DDHHMM<venue>[u].…`) are also saved by users outside the site, so they count as storage too.

### Do not

- Rename, delete or change the format of any key above.
- Change how a screening ID is built: `<date>_<HH:MM>_<venue>` (e.g. `2026-11-26_19:00_Z1`).
- Change the date, time or venue of an existing row in `RAW`. That changes its ID, and picks for it are silently dropped (`readStore` drops unknown IDs and the next `edit` writes them away).
- Remove rows from `RAW` that people may have picked.
- Rename venue codes (`Z1`, `Z2`, `NC`, `CT`) or change the backup-code letters in `VC`/`VR`.
- Change the backup-code format or what `parseCode` accepts.
- Write the in-memory picks to storage directly. Always go through `edit()`, which re-reads storage first so other open tabs don't lose changes.
- Clear storage or "reset" users' data.

### Safe to change

- Film info (`FILMS`), synopses, ratings, links, booking links, `ELC`, `BOOKING_FORM_CT`, `PROGRAMS`.
- Layout, styles, text, and new features.
- Adding new screenings to `RAW`.
- New keys under a new name. Just add them; never repurpose an old key.

### If a screening really moves

Don't edit the old row in place. Add the new row, then add a small one-time migration that moves a saved pick from the old ID to the new ID inside `edit()`. Keep the old ID readable so backup codes still restore.
