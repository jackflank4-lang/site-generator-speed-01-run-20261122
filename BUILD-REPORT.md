# site-generator-speed-01-run-20261122 — Build Report (round 1)

## Entry

Faster drop-in for makefun-site 1.0.0. `solve.js` re-implements the reference
`build(input)` exactly; `main.ts` is the house driver, kept as shipped.

## Verification

- Practice set: **3/3 cases identical** to the oracle (expected.json).
- Differential test on 2 extra generated sets (fresh drand-style seeds, drawn with
  the kit's own `program/generator/draw.ts`): **3/3 each, 9/9 total identical**.
- Driver end-to-end (`main.ts` on the practice cases): **3/3**.
- Kit local check: **5 passed, 0 failed**.

## Speed (practice set, 3 cases, ~250–490 files each)

| set | mine (median of 5) | baseline (median of 5) | speedup |
|---|---|---|---|
| practice | ~210 ms | ~560–795 ms | ~2.3–2.7x |
| gen seed 1 | 207 ms | 571 ms | 2.76x |
| gen seed 2 | 241 ms | 643 ms | 2.67x |

Comfortably above the bar of beating makefun-site 1.0.0 at 95% confidence.

## Optimizations (answers unchanged)

- Inline Markdown escapes plain runs in bulk (one native `indexOf` scan per
  special kind) instead of one regex call per character.
- `slugify` results cached in a module-level Map (tag names are slugified
  repeatedly across posts).
- Markdown lexer dispatches on the line's first character before touching a
  regex; `trimLine`/`isBlank` are manual scans.
- Card objects computed once per page (the reference recomputes them in
  `pageOf`); `\r` normalisation skipped when a file holds no `\r`.
- `renderMarkdown` joins blocks with one `join` instead of a map + join.

## Revision evidence

- `solve.js` written in 4 chunks, then edited in a separate turn: the
  `renderMarkdown` block join was changed from `blocks.map(b => b + '\n').join('')`
  to a single `join`; re-tested after the edit (practice 3/3, gen1 3/3, 2.64x).

## Files

`solve.js` (the entry), `main.ts` (house driver), `README.md` (3 make.fun
badge lines verified character-by-character), `LICENSE` (MIT, SPDX),
`.makefun/owner.json` (wallet `5rM8m83Gjp53ACVY6fzE1HJCWeH34wAtb7F2Pxviec2s`).
Repo total ~60 KB (cap 4 MiB).
