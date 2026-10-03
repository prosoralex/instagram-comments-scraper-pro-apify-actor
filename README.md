# Instagram Comments Scraper — Posts & Reels — Apify Actor usage guide

[![Run on Apify](https://img.shields.io/badge/Apify-Run%20this%20Actor-24C1E0)](https://apify.com/leadsbrary/instagram-comments-scraper-pro?fpr=aupara)
[![Try Apify free](https://img.shields.io/badge/Apify-Try%20it%20free-24C1E0)](https://console.apify.com/sign-up?fpr=aupara)

Instagram comments scraper: export top-level comments of public posts and reels without login. Returns text, date, likes, author, mentions and hashtags as JSON, CSV or Excel. From $0.40 per 1,000.

> **This repository does not contain the Actor's source code.** The Actor is closed-source and
> runs on Apify's infrastructure. This repo is documentation plus ready-to-run client examples
> (curl, Python, JavaScript) that call the hosted Actor with **your own** Apify API token.

**Run it on Apify →** [https://apify.com/leadsbrary/instagram-comments-scraper-pro?fpr=aupara](https://apify.com/leadsbrary/instagram-comments-scraper-pro?fpr=aupara)

## What it does

This **Instagram comments scraper** exports the top-level comments of public Instagram posts and reels without any login. Paste post or reel URLs and get one row per comment: text, date, likes, author username, author ID, verified flag, profile link, mentions and hashtags. Download the data as JSON, CSV, Excel, XML or HTML, or pull it through the Apify API.

It reuses Instagram's own public web data over plain HTTP (no browser launched for the extraction), starts on Apify datacenter proxy and automatically switches to the residential pool if Instagram refuses it.

**Not included:** nested replies to comments are not collected (they are not exposed to logged-out visitors). Only top-level comments are returned.

## Use cases

- Engagement and sentiment analysis of comments on branded posts and campaigns
- Lead spotting: finding buying-intent comments (e.g. 'price?', 'where to buy') under own or competitor posts
- Competitor post monitoring: tracking what audiences say under competitors' launches
- Giveaway and contest auditing: exporting public comments to verify entries
- Feeding comment threads into LLM/AI pipelines for topic clustering and summarization


## Input

| Field | Type | Required | Description |
|---|---|---|---|
| `postUrls` | array | yes | Public Instagram post or reel URLs (/p/, /reel/, /tv/) or bare shortcodes. Duplicates are removed. Profile or story URLs produce an unbilled error row. |
| `maxCommentsPerPost` | integer | no | Maximum number of top-level comments collected per post. |
| `maxResults` | integer | no | Hard cap on the total number of comment rows in this run, across all posts (also protects your spending). |
| `proxyConfiguration` | object | no | Default: Apify datacenter proxy, automatically switching to the residential pool if Instagram refuses it. Choose a proxy group explicitly to disable the automatic switch. |

The full input form (with defaults and validation) is on the [Actor page](https://apify.com/leadsbrary/instagram-comments-scraper-pro?fpr=aupara).


## Output (sample item)

Sample trimmed for readability (long texts and lists are shortened) — a real run returns the complete data.

```json
{
  "postUrl": "https://www.instagram.com/p/DdWyDLcGiqo/",
  "postShortcode": "DdWyDLcGiqo",
  "commentId": "18227451247332690",
  "text": "Почему ии говорит что это ии 🤣🤣🤣 столько времени ушло на создание видео?)))",
  "createdAt": "2026-10-03T11:18:10.000Z",
  "likesCount": 0,
  "postCommentsTotal": 1783,
  "ownerUsername": "leitodenis",
  "ownerId": "289198816",
  "ownerIsVerified": false,
  "ownerProfilePicUrl": "https://scontent-lga3-1.cdninstagram.com/v/t51.2885-19/67402987_530362117733651_7491429738917920768_n.jpg?stp=dst-jpg_s150x150_tt6&_nc_cat=109&ccb=7-5&_nc_sid=f7ccc5&efg=eyJ2ZW5jb2RlX3RhZyI6InByb2ZpbGVfcGljLnd3dy4xMDY0LkMzIn0%3D&_nc_ohc=RV7ki9LJjI8Q7kNvwESG105&_nc_oc=AdrnnCsi470i…",
  "ownerProfileUrl": "https://www.instagram.com/leitodenis/",
  "mentions": [],
  "hashtags": [],
  "scrapedAt": "2026-10-03T17:36:31.650Z"
}
```

## Pricing

Pay only for results: about **$0.60 per 1,000 results** on the Apify Free plan, down to **$0.40 per 1,000** on paid Apify plans.

Apify platform usage (compute and residential proxy traffic) is billed by Apify on top of the per-result price — about **$0.00 per 1,000 results** in our measured test runs (estimate, varies with input). Tip: keep only the output formats and options you need to reduce it.

Prices can change — the [Actor page](https://apify.com/leadsbrary/instagram-comments-scraper-pro?fpr=aupara) always shows the current pricing.

## Quick start

You need an Apify account and an API token (Apify Console → **Settings → API & Integrations**).
Never commit your token: export it as the `APIFY_TOKEN` environment variable.

### cURL

```bash
curl -X POST "https://api.apify.com/v2/acts/leadsbrary~instagram-comments-scraper-pro/run-sync-get-dataset-items" \
  -H "Authorization: Bearer $APIFY_TOKEN" \
  -H "Content-Type: application/json" \
  -d @- <<'JSON'
{
  "postUrls": [
    "https://www.instagram.com/p/DdWyDLcGiqo/"
  ],
  "maxCommentsPerPost": 20,
  "maxResults": 3,
  "proxyConfiguration": {
    "useApifyProxy": true
  }
}
JSON
```

### Python (`apify-client`)

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run_input = {
    "postUrls": [
        "https://www.instagram.com/p/DdWyDLcGiqo/"
    ],
    "maxCommentsPerPost": 20,
    "maxResults": 3,
    "proxyConfiguration": {
        "useApifyProxy": True
    }
}

run = client.actor("leadsbrary/instagram-comments-scraper-pro").call(run_input=run_input)
for item in client.dataset(run["defaultDatasetId"]).iterate_items():
    print(item)
```

### JavaScript (`apify-client`)

```javascript
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const runInput = {
  "postUrls": [
    "https://www.instagram.com/p/DdWyDLcGiqo/"
  ],
  "maxCommentsPerPost": 20,
  "maxResults": 3,
  "proxyConfiguration": {
    "useApifyProxy": true
  }
};

const run = await client.actor('leadsbrary/instagram-comments-scraper-pro').call(runInput);
const { items } = await client.dataset(run.defaultDatasetId).listItems();
console.log(items);
```

Runnable versions live in [`examples/`](./examples).

## Troubleshooting

- **401 / "User was not found or authentication token is not valid"** — check that `APIFY_TOKEN` is set and copied completely.
- **The run finished but the dataset is empty** — widen your search input or check the run log on the Apify Console.
- **Run stops early** — you may have reached the maximum cost per run you set in Apify; raise it in the run options.
- **Need something else?** Open an issue in this repository or use the Issues tab on the Actor page.

## Don't have an Apify account yet?

[Sign up here](https://console.apify.com/sign-up?fpr=aupara) — new accounts get $5 of free platform usage every month, enough to try most Actors without paying upfront.
Browse the full [Apify Store](https://apify.com/store?fpr=aupara) or [all Actors from this developer](https://apify.com/leadsbrary?fpr=aupara).

## Links

- Actor page: [https://apify.com/leadsbrary/instagram-comments-scraper-pro?fpr=aupara](https://apify.com/leadsbrary/instagram-comments-scraper-pro?fpr=aupara)
- All Actors from this developer: [https://apify.com/leadsbrary?fpr=aupara](https://apify.com/leadsbrary?fpr=aupara)
- Apify API reference: [https://docs.apify.com/api/v2](https://docs.apify.com/api/v2)

## Keywords

instagram comments scraper, instagram comment scraper, instagram comment export, export instagram comments to csv, scrape instagram comments, instagram reel comments scraper, instagram comments api, instagram comments no login, instagram giveaway comment picker export, instagram sentiment analysis data

#instagram #instagramscraper #comments #socialmedia #webscraping #sentimentanalysis #leadgeneration #apify #socialmediamarketing #dataextraction

## License

The example code in this repository is released under the MIT License — see [LICENSE](./LICENSE).
It does not cover the Actor itself, which remains closed-source and is operated by its developer on Apify.
