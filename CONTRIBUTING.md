# Contributing

Corrections and additions are welcome — this list is only useful if it's accurate.

## Reporting a new crawler

Open an issue including:

- **User-agent string** exactly as it appears in server logs
- **Operator** (the company running it)
- **Purpose** — training, search/answers, or both
- **Source** — official documentation if it exists, otherwise a description of
  where you observed it (log samples with the IP redacted are fine)

## Correcting a compliance rating

`robotsCompliance` reflects observed behavior, not just vendor claims, so
evidence matters more than opinion. Useful evidence:

- Operator documentation that changed
- A published investigation (Cloudflare, security researchers, etc.)
- Reproducible logs showing a bot fetching a disallowed path

Please link the source. Ratings without sourcing can't be verified, so they
won't be merged.

## Data changes

`ai-crawlers.json` and `ai-crawlers.csv` are generated snapshots — please don't
edit them directly in a PR. Open an issue instead and the change will be made
upstream, which regenerates both files and the per-bot pages together.

The always-current version lives at:
https://geoprompttracker.com/data/ai-crawlers.json
