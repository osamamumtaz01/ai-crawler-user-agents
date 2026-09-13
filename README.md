# AI Crawler User Agents

An open, machine-readable list of every known **AI crawler and agent user-agent string** — GPTBot, ClaudeBot, PerplexityBot, Google-Extended, Bytespider, and 23 more — with each bot's operator, what it's actually for, and whether it honors `robots.txt` in practice.

Available as [JSON](ai-crawlers.json) and [CSV](ai-crawlers.csv). **CC BY 4.0** — free to use commercially, just keep the attribution.

📖 Human-readable version with per-bot pages and copy-paste `robots.txt` rules: **[geoprompttracker.com/bots](https://geoprompttracker.com/bots)**

---

## Why this exists

Most `robots.txt` files mention Googlebot and nothing else. But AI companies run **separate crawlers for different jobs**, and blocking one doesn't block the others:

- **Training crawlers** (`GPTBot`, `ClaudeBot`, `Google-Extended`) collect content to train future models.
- **Search/answer crawlers** (`OAI-SearchBot`, `PerplexityBot`, `Claude-SearchBot`) index or fetch pages so an assistant can **cite** them in live answers.

Blocking a training crawler is an opt-out of model training. Blocking a search crawler removes you from AI answers entirely — the AI-era equivalent of de-indexing yourself. Very different decisions, often confused.

This dataset exists so you can make that decision programmatically instead of copy-pasting from blog posts.

## Quick start

```bash
# Always-current version, straight from the source
curl -s https://geoprompttracker.com/data/ai-crawlers.json
```

**Python**

```python
import json, urllib.request

url = "https://geoprompttracker.com/data/ai-crawlers.json"
data = json.load(urllib.request.urlopen(url))

# Every bot that ignores or only partially respects robots.txt
for bot in data["crawlers"]:
    if bot["robotsCompliance"] in ("no", "partial", "unknown"):
        print(bot["userAgent"], "-", bot["robotsNote"])
```

**JavaScript**

```js
const res = await fetch("https://geoprompttracker.com/data/ai-crawlers.json");
const { crawlers } = await res.json();

// Generate a robots.txt block for training crawlers only
const rules = crawlers
  .filter((b) => b.purpose === "training")
  .map((b) => `User-agent: ${b.userAgent}\nDisallow: /`)
  .join("\n\n");

console.log(rules);
```

**Detect AI crawlers in your logs / middleware**

```js
const AI_AGENTS = crawlers.map((b) => b.userAgent);

function isAiCrawler(userAgentHeader = "") {
  return AI_AGENTS.some((ua) =>
    userAgentHeader.toLowerCase().includes(ua.toLowerCase())
  );
}
```

## Schema

Each entry in `crawlers[]`:

| Field | Type | Description |
|---|---|---|
| `userAgent` | string | The token to match in `robots.txt` and server logs (e.g. `GPTBot`) |
| `slug` | string | URL-safe identifier |
| `operator` | string | Company running the crawler (OpenAI, Anthropic, Google…) |
| `purpose` | `training` \| `search` \| `both` | What the crawler feeds |
| `description` | string | One-line summary |
| `robotsCompliance` | `yes` \| `partial` \| `no` \| `unknown` | Whether it honors `robots.txt` **in practice** |
| `robotsNote` | string | The nuance behind that rating, with sourcing |
| `docsUrl` | string \| null | Operator's official documentation, when published |
| `ipRangeUrl` | string \| null | Operator-published IP-range JSON, where one exists — `null` means the agent can only be identified by its (spoofable) user-agent string |
| `crawlDelay` | `yes` \| `no` \| null | Whether the operator documents `crawl-delay` support — see the note below |
| `crawlDelayNote` | string \| null | The operator's own wording, where they state one |
| `url` | string | Human-readable page for this bot |

Top-level: `name`, `description`, `source`, `documentation`, `license`, `attribution`, `lastVerified`, `count`.

## A note on `crawlDelay`

`crawl-delay` was never part of the robots.txt specification and Google ignores
it, so the usual assumption is that no crawler honors it. That assumption is
mostly right and specifically wrong.

**Only 4 of the 28 operators state a position, and 2 of them support it:**

| Crawler | `crawlDelay` | What the operator says |
|---|---|---|
| YouBot | `yes` | Honors crawl-delay directives |
| ImagesiftBot | `yes` | Reads the value as the minimum seconds between the start of consecutive requests, and documents the interval arithmetic |
| Amazonbot | `no` | "They do not support the crawl-delay directive" |
| Applebot | `no` | "Applebot does not follow crawl-delay" |

For the other 24 the field is `null`, **not** `"no"`. Treat undocumented as
unsupported in practice — but it is an assumption rather than a finding, and
collapsing the two would throw away the distinction. If you want a single
boolean, `crawlDelay === "yes"` is the safe test.

## A note on `robotsCompliance`

This is the field people usually want, and it's the one that requires judgment. It reflects **observed behavior**, not just company claims:

- **`yes`** — operator documents compliance and there's no credible evidence otherwise.
- **`partial`** — compliance is claimed but contradicted in practice, or the bot is a user-triggered fetcher that skips `robots.txt` by design.
- **`no`** — widely reported to ignore `robots.txt`.
- **`unknown`** — undocumented crawler; treat with caution.

`robotsNote` always explains the reasoning. Where a rating is based on third-party investigation rather than operator documentation, the note says so.

**`robots.txt` is a voluntary standard.** It expresses a preference; it does not enforce anything. If blocking genuinely matters to you, pair it with firewall or CDN rules matching the user agent.

## Keeping it current

New crawlers appear constantly. This list is reviewed monthly — see `lastVerified` in the JSON for the date of the last review.

The **[live endpoint](https://geoprompttracker.com/data/ai-crawlers.json) is always the freshest version**; the files in this repo are periodic snapshots. If you're building something that should stay current, fetch the endpoint (it sends permissive CORS headers, so browser-side calls work).

## Contributing

Spotted a missing crawler, or a compliance rating that's out of date? Open an issue with:

- The user-agent string as it appears in logs
- The operator
- A link to official documentation, or evidence of observed behavior

Sourced corrections are very welcome — especially for the `unknown` entries.

## License

**[CC BY 4.0](LICENSE)** — use it anywhere, including commercially. Attribution required:

> AI crawler data from [GeoPromptTracker](https://geoprompttracker.com/bots), CC BY 4.0

## Related

- [Every AI crawler, explained](https://geoprompttracker.com/bots) — per-bot pages with allow/block rules
- [List of AI crawlers and their user agents](https://geoprompttracker.com/guides/list-of-ai-crawlers) — the background guide
- [Should you block AI bots?](https://geoprompttracker.com/guides/should-you-block-ai-bots) — the decision framework
- [AI Crawler Access Checker](https://geoprompttracker.com/tools/ai-crawler-access-checker) — test your own `robots.txt` against this list
