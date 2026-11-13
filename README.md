# OneMore Agency – Content Blocklists

Configuration files that control which content sources and keywords are blocked inside the **OneMore Agency** app. They keep the platform in line with our content policy and with international sanctions and terrorist-designation lists (UN / EU / US).

![Status](https://img.shields.io/badge/status-active-brightgreen)
![Format](https://img.shields.io/badge/format-JSON-blue)
![Policy](https://img.shields.io/badge/type-content%20moderation-orange)

---

## Table of Contents

- [Overview](#overview)
- [Repository Structure](#repository-structure)
- [Configuration Schema](#configuration-schema)
- [Field Reference](#field-reference)
- [Matching Behavior](#matching-behavior)
- [Usage Examples](#usage-examples)
- [Contributing](#contributing)
- [Validation Checklist](#validation-checklist)
- [Security & Compliance](#security--compliance)
- [License](#license)

---

## Overview

This repository contains two JSON configuration files used by OneMore Agency to filter content:

| File | Targets | Identifier type |
|------|---------|-----------------|
| `uuid_blocklist.json` | Radio / media sources | `uuid` |
| `feed_blocklist.json` | Feeds / channels | `feed_id` |

Both files share the same moderation model: allowlist, blocklist by ID, blocklist by domain, keyword rules and exact-match rules. Every blocking rule carries a `reason` so decisions stay auditable.

---

## Repository Structure

```
.
├── uuid_blocklist.json   # Blocklist for UUID-based sources
├── feed_blocklist.json   # Blocklist for feed-based sources
└── README.md
```

> Rename the files above to match the actual file names in your project.

---

## Configuration Schema

Both files follow the same structure. They differ only in the ID fields.

### `uuid_blocklist.json`

```json
{
  "allowlisted_uuids": [],
  "blocked_uuids": [],
  "blocked_domains": [],
  "blocked_keywords": [
    { "term": "example", "reason": "Reason for blocking" }
  ],
  "exact_blacklist": [
    { "term": "Example Channel", "reason": "Reason for blocking" }
  ]
}
```

### `feed_blocklist.json`

```json
{
  "allowlisted_feed_ids": [],
  "blocked_feed_ids": [],
  "blocked_domains": [],
  "blocked_keywords": [
    { "term": "example", "reason": "Reason for blocking" }
  ],
  "exact_blacklist": [
    { "term": "Example Channel", "reason": "Reason for blocking" }
  ]
}
```

---

## Field Reference

| Field | Type | Description |
|-------|------|-------------|
| `allowlisted_uuids` / `allowlisted_feed_ids` | `string[]` | IDs that are always allowed. |
| `blocked_uuids` / `blocked_feed_ids` | `string[]` | IDs that are always blocked. |
| `blocked_domains` | `string[]` | Domains whose content is blocked. |
| `blocked_keywords` | `{ term, reason }[]` | Keywords that trigger blocking (see [Matching Behavior](#matching-behavior)). |
| `exact_blacklist` | `{ term, reason }[]` | Full names or titles that must match exactly. Supports Arabic and Latin text. |

Every entry in `blocked_keywords` and `exact_blacklist` is an object with:

- **`term`** – the string to match.
- **`reason`** – why it is blocked, used for audit and review.

---

## Matching Behavior

> **TODO:** confirm each rule below against the implementation and update this section.

| Rule | Expected behavior |
|------|-------------------|
| Allowlist vs. blocklist | Allowlisted IDs take precedence over every blocking rule. |
| `blocked_keywords` | Case-insensitive match against names/titles/URLs. Use compact forms without spaces (e.g. `almanar`). |
| `exact_blacklist` | Matches the full title. Use this for names that contain spaces, symbols or Arabic text. |
| Arabic text | Add the spelling variants you want matched (with and without hamza, ta marbuta, etc.), as each is a separate entry. |

**Tip:** keywords are compact, so spaced or reordered variants (`al manar`, `al-manar radio`) belong in `exact_blacklist`.

---

## Usage Examples

**Block a keyword**

```json
{ "term": "examplekeyword", "reason": "Designated Terrorist Org affiliation (UN/EU/US)" }
```

**Block a channel by exact name (Arabic or Latin)**

```json
{ "term": "Example Channel | قناة مثال", "reason": "Blocked by app content policy" }
```

**Block a feed by ID**

```json
{
  "blocked_feed_ids": ["feed_12345"]
}
```

---

## Contributing

1. Create a branch: `git checkout -b blocklist/add-<short-description>`
2. Edit the relevant JSON file.
3. Always include a clear `reason`.
4. Validate the JSON (see below).
5. Open a Pull Request describing what was added or removed and why.

### Commit message convention

```
blocklist: add <term> to exact_blacklist
blocklist: remove <term> from blocked_keywords
docs: update README
```

---

## Validation Checklist

Before merging, make sure that:

- [ ] The file is valid JSON (no trailing commas).
- [ ] Every entry has both `term` and `reason`.
- [ ] No duplicate terms exist within the same list.
- [ ] The term is placed in the correct list (keyword vs. exact).
- [ ] Arabic terms are saved as UTF-8.

Quick validation:

```bash
python -m json.tool uuid_blocklist.json > /dev/null && echo "OK"
python -m json.tool feed_blocklist.json > /dev/null && echo "OK"
```

---

## Security & Compliance

- Blocking reasons referencing UN/EU/US designations should be verified against the official lists before being added.
- Do not store personal data in these files.
- Changes should go through code review before reaching production.

---

## License

Internal use only – © OneMore Agency. All rights reserved.
