# OneMore Agency – Content Blocklists

Internal JSON configuration files that control which content sources and keywords are blocked inside the OneMore Agency app.

- `blocklist.json`: blocklist for UUID-based sources.
- `podcast_blocklist.json`: blocklist for feed-based sources.

Both files use the same structure: an allowlist and blocklist of IDs, a list of blocked domains, keyword rules, and exact-match rules. Every keyword and exact-match entry has a `term` and a `reason`.

## Blocking Policy

Blocking is based on the OneMore Agency app's content policies. We block content that we believe is inciting, terrorist-related, or sexual, as well as any other content we consider violent or otherwise unsuitable for our app.
