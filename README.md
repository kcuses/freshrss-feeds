# FreshRSS Personal News & Research Feeds

*** Ready-to-import OPML files are included for both news and research feeds.

A small, reusable collection of **FreshRSS** feed configurations and setup instructions for building a self-hosted personal news and research reader.

The repository is designed around:

- FreshRSS
- Docker
- local, self-hosted operation
- RSS/Atom feeds from publishers
- PubMed RSS feeds for research discovery
- OPML files for easy import

No paid hosting is required.

## Repository contents

```text
freshrss-github-repo/
├── README.md
├── LICENSE
├── .gitignore
├── CHANGELOG.md
├── docs/
│   ├── news-setup.md
│   ├── research-setup.md
│   └── sources.md
└── opml/
│   ├── freshrss-news.opml
│   ├── freshrss-research.opml
│   └── freshrss-all.opml
│
└── images/
│   ├── example_news1.png
│   ├── example_research1.png
│   └── example_research2.png

```

## Choose a setup

### News

The news collection is designed around:

- international/world news
- European news
- Danish news
- optional Nordic and local coverage

The news OPML can be imported directly into FreshRSS.

See:

[`docs/news-setup.md`](docs/news-setup.md)

### Research

The research collection combines:

- direct journal RSS/Atom feeds
- PubMed journal feeds
- PubMed topic searches

This allows a reader to follow both specific high-value journals and papers appearing anywhere in the literature that match selected topics.

See:

[`docs/research-setup.md`](docs/research-setup.md)

## Quick start

1. Install Docker Desktop.
2. Create a FreshRSS project directory.
3. Start FreshRSS with Docker Compose.
4. Open `http://localhost:8080`.
5. Complete the FreshRSS initial setup.
6. Import the relevant OPML from `opml/`.
7. Refresh feeds once.
8. FreshRSS will then update automatically according to the configured schedule.

The setup guides contain the Windows and macOS instructions.

## FreshRSS interface

### News and research dashboard

![FreshRSS main view](images/example_research1.png)

### News feeds

![FreshRSS news view](images/example_research2.png)

### Research feeds

![FreshRSS research view](images/example_news1.png)

## OPML files

### News

`opml/freshrss-news.opml`

Contains the reusable news subscription collection.

### Research

`opml/freshrss-research.opml`

Contains the reusable research subscription collection.

### Combined

`opml/freshrss-all.opml`

Contains both news and research subscriptions.

## Local news

The news collection intentionally keeps local news flexible.

A user can add local sources through:

- regional public broadcasters
- local newspapers
- police/public-service RSS feeds
- municipal or public-agency feeds
- Google News RSS searches when a direct RSS feed is unavailable

See [`docs/news-setup.md`](docs/news-setup.md) and [`docs/sources.md`](docs/sources.md).

## Updating the feed collection

RSS endpoints can change over time.

When a feed breaks:

1. Check the publisher's current website for an official RSS/Atom feed.
2. Prefer the first-party feed when available.
3. If a journal has no reliable RSS, consider a PubMed RSS search where appropriate.
4. Update the OPML file.
5. Record the change in `CHANGELOG.md`.


Only the reproducible configuration, documentation, and OPML feed lists belong in this repository.

## License

This repository's original documentation and configuration are released under the MIT License. Third-party publisher content, names, trademarks, and feed material remain subject to their respective owners and terms.
