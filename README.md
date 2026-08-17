# Bible Race Verses

The verse library for [Bible Race](https://edoyama.com/biblerace), Ed Oyama's Sunday school verse-memorization game. `edoyama.com/api/verses` reads `verses.json` from this repo's `main` branch at request time, so **pushing here updates the live game. No site deploy.**

This repo is public on purpose: the verses render publicly in the game anyway, and a public repo means the API reads it with zero credentials.

## Add this week's verse (the whole job)

1. Edit `verses.json`: append the new verse as the **last** entry of the `verses` array.
2. Commit and push to `main`.

That's it. The API reverses the array, so the last entry becomes **VERSE OF THE WEEK** on every device within ~10 minutes (edge cache 5 min + GitHub raw cache ~5 min).

```bash
cd ~/bible-race-verses && git pull --rebase
# edit verses.json: append to the END of the array
git add verses.json && git commit -m "Verse of the week: <ref>" && git push
```

For Claude sessions on any of Ed's machines: **always `git pull --rebase` first** (both the M4 and the M2 can write here), and never reorder existing entries. Append only.

## Rules the API enforces (a bad row is skipped, never shipped)

- `ref` must contain a digit (a chapter:verse), max 40 chars, e.g. `"Philippians 4:13"`.
- `text` max 400 chars, at least 2 words.
- Exact duplicates collapse. Max 60 verses served.
- Malformed JSON means the API serves nothing and the game falls back to its cached list, then to its built-in verses. A typo here can never break the game.

## Verify after pushing

```bash
curl -s https://edoyama.com/api/verses | python3 -c "import sys,json; print(json.load(sys.stdin)['verses'][0])"
```

Should print the verse you just added (allow ~10 min for caches).
