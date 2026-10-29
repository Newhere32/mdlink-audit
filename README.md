# Markdown Link Audit (`mdlink-audit`)

A local Markdown link and anchor checker designed for documentation repositories and CI. It validates relative file paths, Markdown heading fragments, explicit HTML anchors, inline links/images, and common reference-style links. It makes no network requests, so it is fast and deterministic offline.

## Features

- Scans a single Markdown file or a directory tree.
- Checks relative links and image references for missing local targets.
- Validates fragments against generated heading slugs and explicit HTML `id`/`name` anchors.
- Handles duplicate heading slugs (`#setup`, `#setup-1`), percent-encoded paths, and reference-style links.
- Skips fenced code blocks and inline code spans to reduce false positives in docs that show examples.
- JSON reporting, glob excludes, and opt-in CI failure.

## Install and use

Requires Python 3.10+; no runtime dependencies.

```bash
python -m pip install -e .
mdlink-audit docs/ --fail-on-broken
mdlink-audit README.md --format json --output links-report.json
mdlink-audit . --exclude 'docs/generated/**' --fail-on-broken
```

Exit codes: `0` by default even when issues are reported (useful for discovery); `1` when `--fail-on-broken` is set and broken links exist; `2` for invalid input or an operational error.

## Scope and limitations

- Only local targets are checked. External HTTP/HTTPS URLs are intentionally not fetched, avoiding network dependence, rate limits, and accidental requests to private endpoints.
- This uses a focused parser rather than implementing the entire CommonMark/GFM grammar. Complex nested parentheses in link destinations, raw HTML navigation, and extensions may need a full Markdown parser.
- Directory links are accepted when the directory exists; the checker does not guess a server's directory-index behavior.
- Fragments are validated only for Markdown files included in the scan. Fragments into non-Markdown files are not inspected.
- Fenced code and inline code are ignored; examples containing literal Markdown links will not be treated as documentation links.

## Development

```bash
python -m pip install -e .
python -m compileall -q src tests
python -m unittest discover -s tests -v
```
## Project status

This is an AI-assisted initial implementation released as an alpha. Its parser intentionally supports a documented subset of Markdown rather than all CommonMark/GFM edge cases; validate it against the conventions used by your documentation site.
