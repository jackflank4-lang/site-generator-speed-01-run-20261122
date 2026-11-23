[![make.fun](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/jackflank4-lang/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/jackflank4-lang/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/reward?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

# Fast makefun-site drop-in

`run(input)` returns exactly what makefun-site 1.0.0's `build` returns for one
site's input, but faster: about 2.6x the house baseline's speed on the practice
set (median ~210ms vs ~560ms for 3 cases).

## Run

```sh
node main.ts
```

`main.ts` reads every case from `/input/cases.json` (or `$MAKEFUN_INPUT_DIR`),
answers each with `run` from `solve.js`, and writes `/output/answers.json`
(or `$MAKEFUN_OUTPUT_DIR`).

## What it does

` solve.js` re-implements makefun-site 1.0.0's build pipeline, byte for byte:

- sources sorted by path, `\r\n` / lone `\r` made `\n`; `.md` sources, the rest static
- front matter (the strict `key: value` grammar, its seven keys and every refusal reason)
- permalinks: slugs made unique per directory, `posts`/`tags`/`page` reserved at the root
- Markdown: the house subset (fences, ATX headings with unique ids, `---`/`***` breaks,
  blockquotes, flat lists, paragraphs; code spans, strong, emphasis, links, escapes)
- collections (newest first, equal dates by slug), tags (by count, then slug),
  layouts (`post`/`page`/`index`/`tag`/`tags`), static files (shadowed ones reported)
- `build-report.txt`: every refusal, draft, broken link and unparsed template, sorted,
  then the counts

## Speed

The hot paths are tightened without changing any answer:

- inline Markdown escapes plain runs in bulk instead of one regex call per character
- `slugify` results are cached (tag names are slugified over and over)
- the Markdown lexer dispatches on the line's first character before touching a regex
- card objects are computed once per page instead of twice
- `\r` normalisation is skipped when a file holds no `\r`

## Verify

```sh
node cli/makefun.mjs check --kit <kit-dir> --repo .
```

## License

MIT. See [LICENSE](LICENSE).
