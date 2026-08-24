# Feed Sources and Curation

This document explains the principles used to build the News and Research feed collections.

## News

The news collection is intended to provide a balanced personal reading feed rather than exhaustive coverage of every publication.

### International

Use a small set of strong international sources.

Typical choices include:

- Reuters
- Associated Press
- BBC World
- Al Jazeera
- DW
- France 24

The goal is to provide multiple perspectives and broad coverage without creating excessive duplication.

### Europe

Prefer pan-European or international feeds over subscribing to every country's national news.

Typical choices include:

- BBC Europe
- DW
- France 24
- Euronews
- Politico Europe
- Reuters
- Associated Press
- Guardian World

This makes the Europe section useful for major European events without overwhelming the reader with country-specific articles.

### Denmark

A good baseline is:

- DR
- one or two Danish newspapers

Possible newspapers include:

- Politiken
- Berlingske
- Information

The exact selection should reflect the user's preferred editorial perspectives.

### Nordics

A single strong English-language Nordic aggregator can be sufficient when the goal is to follow important Nordic developments rather than read local reporting from every Nordic country.

### Local news

Local feeds should be selected based on the user's location.

Useful options include:

1. Regional public broadcaster feeds.
2. Local newspaper RSS/Atom feeds.
3. Police or other public-service RSS feeds.
4. Municipal/public-agency feeds.
5. Google News RSS searches when no direct RSS exists.

A typical local setup might be:

```text
Denmark
  └── national news

Copenhagen / Local
  ├── regional broadcaster
  ├── local newspaper
  └── local public-service feed
```

Users outside Copenhagen can replace the local category with their own city or region.

## Research

Research feeds use two complementary approaches.

### 1. Direct journal feeds

When a journal provides a reliable official RSS/Atom feed, use it directly.

Preferred order:

```text
Official journal RSS
        ↓
Official journal Atom
        ↓
PubMed RSS fallback
```

Avoid relying on third-party RSS conversion services when a first-party feed exists.

### 2. PubMed topic feeds

PubMed can generate RSS feeds from searches.

This is useful for research areas that span many journals.

Typical examples:

- machine learning + epidemiology
- inflammatory bowel disease + computational methods
- multi-omics + biomarkers
- causal inference + population health

A good topic feed should be broad enough to discover new work but narrow enough to avoid becoming a high-volume stream of irrelevant papers.

## Broken feeds

RSS endpoints change.

When a feed stops updating:

1. Visit the publisher's current website.
2. Check for RSS, Atom, Subscribe, or Alerts.
3. Prefer the current first-party feed.
4. If appropriate, create a PubMed journal/topic RSS feed.
5. Remove the obsolete endpoint from the OPML.
6. Record the replacement in `CHANGELOG.md`.

## OPML

OPML files in this repository contain subscription URLs and category structure.

They do not contain:

- FreshRSS passwords
- FreshRSS databases
- article history
- user accounts
- private reading state

OPML files are therefore suitable for sharing as reusable subscription configurations, subject to the terms of the underlying feed providers.
