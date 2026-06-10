# Script engineering — the YOUSPEAK glyph pipeline

> How the cathedral's writing system is built and kept honest. Agent-runnable:
> every step is a command, and a drift-guard makes the failure mode impossible.

## The script

YOUSPEAK is a compositional-logographic script: one glyph per morpheme, words are
linear sequences of morpheme-glyphs, Latin transliteration is the canonical
machine representation, and glyphs are a display layer that degrades gracefully
to Latin when the font is absent. Codepoints live in the Unicode Private Use
Area (`U+E100–U+E1FF`, extensible to `U+E2FF`).

## How a glyph is made (procedural, not hand-drawn)

Glyphs are defined as **stroke lists in Python**, not SVG or hand-drawn outlines.
Each spec is a set of line segments (and optional filled polygons) on a 1000-EM
square, at an 80-EM monoline stroke weight, with a donor-tradition sigil at
top-left and a morpheme-class mark at bottom-right. `build_font_v1.py` converts
strokes to filled contours and emits an OpenType font with the correct PUA cmap.

```
python3 script/tools/build_font_v1.py        # specs → youspeak-v1.otf
python3 script/tools/render_preview_v1.py    # specs → SVG/HTML previews
```

The design discipline (six axioms, six cohesion rules) constrains every glyph:
points-beyond-itself, teaches-by-shape, belongs-to-its-tradition, stable-once-
drawn, legible-at-8pt, self-complete; plus shared stroke weight, monoline,
shape-families bound to semantic domains, and mandatory silhouette distinctness.

### Worked example: the `-me` glyph (`U+E12A`)

`-me` (Sumerian *me*, "received-ordinance") is the most productive suffix (25+
members) and carries the language's core theological claim. Its glyph is
**descent-into-reception**: a downward triangle (the ordinance descends) landing
in an upward-opening cup (the vessel receives). It rhymes visually with `doxa`
(also a descent-triangle) but is distinguished at 8pt — `doxa` has a crossbar
(manifestation-that-shows), `-me` has a cup (gift-that-lands).

## The drift-guard (why a glyph can never sit unbuilt)

The failure mode this pipeline was built to kill: a glyph **designed in the spec
but absent from the built font** — invisible, with no alarm. `-me` had sat that
way for three weeks. The fix is a verifier plus a git hook:

- `check_integrity.py` verifies the whole chain — orphan specs, designed-but-
  unbuilt glyphs, stale font codepoints, installed-font freshness (sha256 vs
  repo), and dashboard counts vs derived truth. Strict by default; a
  `--pre-commit` mode softens what isn't build-chain.
- A `pre-commit` hook (wired via `core.hooksPath`) rebuilds the font and previews
  whenever glyph sources are staged, stages the rebuilt artifacts, then runs the
  integrity check — and **blocks the commit** if the chain is incoherent. The
  three-week gap is now structurally impossible.

## The front door

One command tends the whole organ: `cathedral font` (build → previews → install
→ verify), `cathedral check` (drift), `cathedral status` (organs + site + fleet
pulse), `cathedral serve`. Frictionless rule: anything you must remember more
than once becomes a verb.

## Honest flags

- Glyph specs are procedural stroke lists, not vector-refined outlines — correct
  silhouettes, but a type designer would refine curves before print.
- The font is PUA-encoded; it renders only where the font is installed or
  `@font-face`-loaded. Latin transliteration is the fallback by design.
