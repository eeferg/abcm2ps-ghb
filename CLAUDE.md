# abcm2ps — Ed's working fork

## What this is

**abcm2ps** converts ABC music notation (a text-based format for folk/traditional tunes) into
PostScript or SVG sheet music files. This fork is used to typeset bagpipe sheet music.

The upstream author is **Jean-François Moine** (http://moinejf.free.fr), who is the sole
contributor throughout the entire git history.

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

### Quick start

```sh
./configure
make
```

The binary is produced in the current directory as `./abcm2ps`.

### Dependencies — Ubuntu 24.04 LTS

Install all required development libraries with a single command:

```sh
sudo apt install \
    libpango1.0-dev \
    libcairo2-dev \
    libharfbuzz-dev \
    libfreetype-dev \
    libfontconfig1-dev
```

Versions confirmed working on Ubuntu 24.04.2 LTS:

| Package | Version |
|---------|---------|
| `libpango1.0-dev` | 1.52.1 |
| `libcairo2-dev` | 1.18.0 |
| `libharfbuzz-dev` | 8.3.0 |
| `libfreetype-dev` | 2.13.2 |
| `libfontconfig1-dev` | 2.15.0 |

You also need a standard C build environment (`gcc`, `make`):

```sh
sudo apt install build-essential
```

### Other Debian/Ubuntu releases

The package names are the same across Debian/Ubuntu. Pango 1.44 or later is required
(the deprecated FreeType API used in older upstream code is fixed in this fork — see below).

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

---

## Repository remotes

```
origin    git@github.com:eeferg/abcm2ps.git   (this fork — push here)
upstream  https://github.com/lewdlime/abcm2ps.git  (archived GitHub source)
```

The live upstream is moinejf's Fossil repo:
https://chiselapp.com/user/moinejf/repository/abcm2ps
