# abcm2ps — Ed's bagpipe edition

## What this is

**abcm2ps** converts ABC music notation (a text-based format for folk/traditional tunes) into
PostScript or SVG sheet music files. This edition is maintained for typesetting bagpipe sheet music.

The original author is **Jean-François Moine** (http://moinejf.free.fr), who is the sole
contributor throughout the entire git history. Full credit for the program belongs to him.

---

## Project history

| Date | Event |
|------|-------|
| 2024-09-08 | Last commit to the GitHub-hosted repo (`leesavide/abcm2ps`), v8.14.15, commit `4b76515` — repo then archived |
| 2025-02-02 | Moine resumed development on his own Fossil repository at https://chiselapp.com/user/moinejf/repository/abcm2ps |
| 2026-01-26 | Latest Fossil snapshot used for comparison: commit `53e682e6675a14f5`, v8.14.18 |
| 2026-06-19 | This fork created at https://github.com/eeferg/abcm2ps — full git history preserved, Fossil changes merged, Pango fix applied |

---

## Stable reference commit

**`cd345d3` — "New release v8.14.3"** is the most tested and stable version for bagpipe
sheet music use. If a newer version causes problems, revert to it:

```sh
git checkout cd345d3
```

To return to the latest:

```sh
git checkout main
```

---

## Building

See the README for full dependency and build instructions (Ubuntu and macOS).
Quick start: `./configure && make` — the binary is `./abcm2ps`.

---

## Changes in this fork

### 1 — Sync upstream Fossil changes (v8.14.15 → v8.14.18)

**Commit `a70427a`** — *Sync upstream changes from Fossil v8.14.18 (2026-01-26)*

After the GitHub repo was archived, moinejf continued development on Chiselapp (Fossil).
A snapshot of his Fossil repo at v8.14.18 was compared against the last GitHub commit
(v8.14.15). The differences were small and applied here to preserve the full git history.

Files changed and what was fixed:

| File | Change |
|------|--------|
| `abcparse.c` | Copyright year; add temperament-equal fallback (`d = 256`) when no denominator given |
| `draw.c` | Copyright year; fix short-accidental condition check (`(n & 255) == 255`) |
| `format.c` | Add `#include <stdbool.h>`; rename local variable `bool` → `boolv` to avoid C keyword conflict |
| `music.c` | Copyright year; remove flawed tuplet voice-combine block |
| `subs.c` | Copyright year; add upstream `if(0)` guard on font-name substitution loop |
| `configure` | Bump to v8.14.18 / 2026-01-28; fix `srcdir` to use `realpath` |
| `README.md` | Remove Travis CI badge; minor text and link updates |

`build.ninja`'s release target was not updated — it migrated to Fossil-specific commands
that do not apply to this git repository.

### 2 — Fix deprecated Pango FreeType API (subs.c)

**Commit `b22d2bf`** — *fix: replace deprecated Pango FreeType API with HarfBuzz in subs.c*

Pango 1.44 deprecated `pango_fc_font_lock_face` / `pango_fc_font_unlock_face` in favour
of the HarfBuzz-based `pango_font_get_hb_font`. On Ubuntu 24.04 (Pango 1.52) the old
calls produce four `-Wdeprecated-declarations` warnings and may stop working in a future
Pango release.

Changes in `subs.c`, functions `pg_line_output` and `pg_para_output`:

| Old (FreeType) | New (HarfBuzz) |
|----------------|----------------|
| `#include <pango/pangofc-font.h>` | `#include <hb-ot.h>` |
| `PangoFcFont *fc_font = PANGO_FC_FONT(font)` | *(removed)* |
| `FT_Face face = pango_fc_font_lock_face(fc_font)` | `hb_font_t *hb_font = pango_font_get_hb_font(font)` |
| `FT_Load_Glyph` + `FT_HAS_GLYPH_NAMES` + `FT_Get_Glyph_Name` | `hb_font_get_glyph_name` |
| `FT_Get_Postscript_Name` (pointer comparison) | `hb_ot_name_get_utf8` with `strcmp` on a local `char[256]` buffer |
| `pango_fc_font_unlock_face(fc_font)` | *(removed — HarfBuzz needs no lock/unlock)* |

Result: clean build with zero deprecation warnings on Ubuntu 24.04.

### 3 — Fix bagpipe gracenote notehead alignment (syms.c)

**In progress** — *fix: align grace note notehead with stem in PostScript output*

The gracenote notehead (`ghd` in `syms.c`) was drawn a few pixels to the left of the stem.
Root cause: the PS definition started the notehead ellipse at `x+1.7` but the stem (`gu`/`gd`
and flagged variants `sgu`/`sgd`/`sgs`) is always drawn at `x + GSTEM_XOFF` = `x+2.3`
(`GSTEM_XOFF` defined in `abcm2ps.h`). The SVG version of the same glyph (`svg.c`) correctly
used `x+2.2`, making the discrepancy PS-only.

**Tuning notes** — the offset in `ghd` (`syms.c:482`, the `RM` argument):

| Value | Result |
|-------|--------|
| `1.7` | original — notehead visibly left of stem (~0.6 unit gap) |
| `2.3` | flush with stem — slightly too far left visually |
| `2.4` | slightly too left |
| `2.45` | current — slightly too right, but closest so far |

Current value in `syms.c`: `2.45 1.5 RM`. Sweet spot is likely between 2.4 and 2.45.

---

## Repository

This edition is maintained at https://github.com/eeferg/abcm2ps.

Moine's original work continues on his Fossil repository:
https://chiselapp.com/user/moinejf/repository/abcm2ps

The archived GitHub source this was based on:
https://github.com/lewdlime/abcm2ps (archived 2024)
