# App Store and Google Play Review Monitor MCP Server

[![Smithery](https://smithery.ai/badge/mambabuilt/mcp-app-store-google-play-review-monitor)](https://smithery.ai/servers/mambabuilt/mcp-app-store-google-play-review-monitor) [![Glama score](https://glama.ai/mcp/servers/mambalabsdev/mcp-app-store-google-play-review-monitor/badges/score.svg)](https://glama.ai/mcp/servers/mambalabsdev/mcp-app-store-google-play-review-monitor) [![MCP Registry](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fregistry.modelcontextprotocol.io%2Fv0%2Fservers%3Fsearch%3Dcom.mambabuilt%252Fmcp-app-store-google-play-review-monitor%26limit%3D1&query=%24.servers%5B0%5D._meta%5B%22io.modelcontextprotocol.registry%2Fofficial%22%5D.status&label=mcp%20registry&color=blue)](https://registry.modelcontextprotocol.io/v0/servers?search=com.mambabuilt/mcp-app-store-google-play-review-monitor&limit=1) [![npm version](https://img.shields.io/npm/v/@mambalabsdev/mcp-app-store-google-play-review-monitor)](https://www.npmjs.com/package/@mambalabsdev/mcp-app-store-google-play-review-monitor) [![npm downloads](https://img.shields.io/npm/dm/@mambalabsdev/mcp-app-store-google-play-review-monitor)](https://www.npmjs.com/package/@mambalabsdev/mcp-app-store-google-play-review-monitor) [![license](https://img.shields.io/github/license/mambalabsdev/mcp-app-store-google-play-review-monitor)](https://github.com/mambalabsdev/mcp-app-store-google-play-review-monitor/blob/main/LICENSE) [![mcpservers.org](https://img.shields.io/badge/mcpservers.org-listed-blue)](https://mcpservers.org/servers/mambalabsdev/mcp-app-store-google-play-review-monitor)

An MCP server for the Mamba Labs App Store and Google Play Review Monitor actor on Apify. Give it apps. It returns their reviews by country store, newest first, and on a schedule only the reviews posted since the last run.

## What's Inside

- [What it does](#what-it-does)
- [Quick start](#quick-start)
- [Prerequisites](#prerequisites)
- [Example prompts](#example-prompts)
- [Tool](#tool)
- [Inputs](#inputs)
- [Pricing](#pricing)
- [Your keys](#your-keys)
- [Full actor documentation](#full-actor-documentation)
- [License](#license)

## What it does

- **Reviews**: Apple App Store and Google Play, in one row layout: stars, title, text, author, app version, date, helpful votes, and the developer reply where Google Play shows one.
- **Only new reviews**: with a `state_key`, each run skips the reviews an earlier run returned, per app and country.
- **Rating trend**: one `app_summary` row per app and country store with the app rating, rating count, the change since the last run, and the star split of the new reviews.
- **Developer domain**: every row carries the developer name, website, and domain.

Input with no app comes back as one free row whose `error_reason` starts with `input_invalid:`.

All of the work runs on Apify. This package is a thin client that starts the actor run, waits for it, and hands back the dataset rows.

## Quick start

You need Node.js 18 or newer and an Apify account with an API token.

Add this to your Claude Desktop config (`claude_desktop_config.json`), Cursor, or any MCP client:

```json
{
  "mcpServers": {
    "mamba-app-store-google-play-review-monitor": {
      "command": "npx",
      "args": [
        "-y",
        "@mambalabsdev/mcp-app-store-google-play-review-monitor"
      ],
      "env": {
        "APIFY_TOKEN": "your-apify-token"
      }
    }
  }
}
```

Restart the client. The `monitor_app_reviews` tool appears.

## Prerequisites

- Node.js 18 or newer.
- An Apify API token from [console.apify.com/account/integrations](https://console.apify.com/account/integrations), set as `APIFY_TOKEN`. The server lists its tool without a token; a call needs one.

## Example prompts

- "Pull the 50 newest App Store and Google Play reviews for Slack in the US."
- "Which 1 and 2 star reviews did Notion get in the UK App Store in the last 30 days?"
- "Check my competitors' apps every morning and tell me what new reviews came in and whether their rating moved."

## Tool

`monitor_app_reviews`. One tool. Example arguments:

```json
{
  "apps": [
    "https://apps.apple.com/us/app/slack/id618783545",
    "com.Slack"
  ],
  "countries": [
    "us"
  ],
  "max_reviews_per_app": "50",
  "state_key": "slack-reviews-daily"
}
```

## Inputs

Every buyer facing input of the actor, unchanged. Omitted inputs take the actor's own defaults. Numbers and booleans are sent as strings.

| Input | Type | Description |
|---|---|---|
| `apps` | array | One per line: an App Store URL (https://apps.apple.com/us/app/slack/id618783545), an App Store id (id618783545), a Google Play URL, or a Google Play package name (com.Slack). Up to 100 apps per run |
| `countries` | array | Two letter store codes, one per line, for example us, gb, de. Default us. Up to 10. App Store reviews are separate per country. Google Play shows the same reviews in every country for one language, so its reviews are returned once, under the first country, and each other country gets its own rating row |
| `max_reviews_per_app` | string | Most reviews returned per app and country store in one run, newest first. Default 100. The App Store feed serves at most 500 per country; Google Play up to 2000 |
| `since` | string | Optional. Only reviews on or after this date (YYYY-MM-DD), or within this many days (30) |
| `star_ratings` | array | Optional. Only return reviews with these star ratings, for example 1 and 2 for complaints. Empty returns all |
| `only_new` | string | Default true. Each run remembers the reviews it returned, per app and country, under the state key |
| `state_key` | string | Names the saved state. Use one key per schedule, for example competitor-apps-daily |
| `reset_state` | string | true forgets the reviews returned before, so this run starts over |
| `include_summary` | string | Default true: one row per app and country store with the rating, rating count, change since the last run, and the stars of the new reviews |
| `language` | string | Language code for Google Play reviews and pages. Default en |
| `max_charge_usd` | string | Optional. The run stops before it would charge more than this |
| `proxy_mode` | string | A reliability choice. Default auto |

## Pricing

Pay per event on Apify, billed to your Apify account. Prices in USD.

| Event | Free plan | Bronze | Silver | Gold |
|---|---|---|---|---|
| `review-row`, one App Store or Google Play review returned | $0.0001 | $9.5e-05 | $9e-05 | $8.5e-05 |
| `app-summary`, one app and country store checked: the app rating, rating count, change since the last run, and the stars of the new reviews. Off when include_summary is false | $0.001 | $0.00095 | $0.0009 | $0.00085 |
| `apify-actor-start`, per run start | $0.0006563 | $0.0006563 | $0.0006563 | $0.0006563 |

Rows for an app or input that could not be read are free.

## Your keys

The actor needs no key. It reads Apple's public iTunes lookup service and reviews feed, and the public Google Play pages, and holds no key of any kind. Proxy traffic adds nothing to your price: Mamba Labs pays the platform costs of the run. `proxy_mode` is a reliability setting.

## Full actor documentation

[apify.com/mambalabs/app-store-google-play-review-monitor](https://apify.com/mambalabs/app-store-google-play-review-monitor)

## License

MIT. See [LICENSE](LICENSE).
