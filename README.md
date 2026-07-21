# Cogniscale

The Cogniscale website is a dependency-free, static one-page site for an independent software product studio. It is published at [cogniscalellc.github.io](https://cogniscalellc.github.io).

## Files

- `index.html`, `styles.css`, and `favicon.svg` — the website and its local visual assets.
- `brand/` — editable SVG source artwork for the business card.
- `tools/render_cards.py` — deterministic renderer for the card PDF and PNG proofs.
- `outputs/` — generated print PDF and PNG proof files.
- `tests/` — static-site and card-artifact checks.

## Preview locally

From the repository root, run:

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000` in a browser.

## Test

```bash
node --test tests/site.test.mjs
PYTHONPATH=/Users/sbrant/.cache/codex-runtimes/codex-primary-runtime/dependencies/python \
/Users/sbrant/.cache/codex-runtimes/codex-primary-runtime/dependencies/python/bin/python3 -m unittest tests/test_card_artifacts.py -v
```

## Regenerate card files

```bash
PYTHONPATH=/Users/sbrant/.cache/codex-runtimes/codex-primary-runtime/dependencies/python \
/Users/sbrant/.cache/codex-runtimes/codex-primary-runtime/dependencies/python/bin/python3 tools/render_cards.py
```
