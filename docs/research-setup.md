# FreshRSS: Self-Hosted Research Article Feed

This guide shows how to build a personal research-article RSS reader with **FreshRSS + Docker**, running locally on either **Windows or macOS**.

The setup is intentionally general. Each user can choose their own journals and PubMed searches.

## What this setup does

```text
Journal RSS feeds ──────────────┐
                                │
PubMed topic RSS feeds ────────┤
                                ↓
                           FreshRSS
                                ↓
                    http://localhost:8080
```

FreshRSS becomes a single research dashboard containing:

- direct feeds from journals that provide RSS/Atom
- PubMed RSS feeds for topics or journals
- categories for organizing journals
- one local, self-hosted reader
- automatic updates at a user-defined interval

No paid hosting is required.

---

# 1. Requirements

## Windows

Windows Home is sufficient when using **Linux containers**.

You need:

- Windows 10/11
- hardware virtualization enabled
- WSL 2
- Docker Desktop

## macOS

You need:

- a supported Intel or Apple Silicon Mac
- Docker Desktop for Mac

WSL is **not** required on macOS.

---

# 2. Install Docker

## Windows

Install Docker Desktop:

https://docs.docker.com/desktop/setup/install/windows-install/

Docker Desktop on Windows should use the **WSL 2 backend** and Linux containers.

After installation, open PowerShell and verify:

```powershell
docker --version
docker compose version
docker run hello-world
```

Also verify WSL:

```powershell
wsl --status
wsl -l -v
```

Your Linux distribution should show:

```text
VERSION
2
```

If WSL is not installed:

```powershell
wsl --install
```

Restart Windows when prompted.

## macOS

Install Docker Desktop:

https://docs.docker.com/desktop/setup/install/mac-install/

Choose the correct download for:

- Apple Silicon
- Intel

Open Docker Desktop and wait until it is running.

In Terminal, verify:

```bash
docker --version
docker compose version
docker run hello-world
```

---

# 3. Create the FreshRSS project directory

## Windows

Open PowerShell:

```powershell
mkdir C:\FreshRSS
cd C:\FreshRSS
```

## macOS

Open Terminal:

```bash
mkdir -p ~/FreshRSS
cd ~/FreshRSS
```

---

# 4. Create `compose.yml`

The same Docker Compose configuration can be used on both Windows and macOS.

## Windows

```powershell
notepad compose.yml
```

## macOS

```bash
nano compose.yml
```

Use:

```yaml
services:
  freshrss:
    image: freshrss/freshrss:latest
    container_name: freshrss
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      TZ: Europe/Copenhagen
      CRON_MIN: "0"
    volumes:
      - ./data:/var/www/FreshRSS/data
      - ./extensions:/var/www/FreshRSS/extensions
```

### Change the timezone

Replace:

```text
Europe/Copenhagen
```

with your own IANA timezone if needed.

Examples:

```text
Europe/Riga
Europe/London
Europe/Berlin
America/Toronto
America/New_York
Australia/Sydney
```

### Update frequency

This guide uses:

```yaml
CRON_MIN: "0"
```

That runs the FreshRSS updater once per hour.

To use a different schedule, change `CRON_MIN`.

For a personal research reader, hourly updates are usually a good starting point.

Save the file.

---

# 5. Start FreshRSS

From the project directory:

```text
docker compose up -d
```

Then verify:

```text
docker compose ps
```

The `freshrss` container should be running.

Open:

```text
http://localhost:8080
```

---

# 6. Complete the initial FreshRSS setup

FreshRSS will open its installation screen.

Choose:

- preferred interface language
- administrator username/password
- **SQLite** for the database

SQLite is convenient for a personal local installation because no separate database server is required.

After setup, FreshRSS will open the main interface.

---

# 7. Build your research categories

A useful general structure is:

```text
Research
├── Core Journals
├── Methods
├── Clinical / Medicine
├── Population Health
├── Biology / Molecular
└── PubMed
```

Alternatively, keep it simpler:

```text
Core Journals
PubMed
```

The important point is to avoid making too many folders at the beginning.

---

# 8. Add journal RSS feeds

For journals that provide a publisher RSS or Atom feed:

1. Open the journal website.
2. Look for:
   - RSS
   - RSS feed
   - Subscribe
   - Feed
   - alerts
3. Copy the RSS/Atom URL.
4. In FreshRSS, open **Subscription management**.
5. Click **Add**.
6. Paste the RSS URL.
7. Assign the feed to the appropriate category.

### Prefer direct publisher feeds

Use:

```text
Journal → official RSS → FreshRSS
```

rather than:

```text
Journal → third-party RSS generator → FreshRSS
```

when the official feed exists.

This generally reduces broken-feed problems and third-party dependencies.

---
# 9. Use PubMed when a journal has no reliable RSS

PubMed is an excellent fallback because it can create RSS feeds from searches.

Go to:

https://pubmed.ncbi.nlm.nih.gov/

Run a search, then use **Create RSS**.

This is useful when:

- the publisher has no public RSS
- the publisher's RSS endpoint is broken
- the journal has moved platforms
- you want to monitor a research topic across multiple journals

The Research OPML in this repository already contains four pre-configured PubMed RSS feeds. The searches used to create them are listed below so that they can be inspected, modified, or regenerated if necessary.

### PubMed IBD and Computational Methods

```text
(
  "Inflammatory Bowel Diseases"[Mesh]
  OR "inflammatory bowel disease"[Title/Abstract]
  OR Crohn*[Title/Abstract]
  OR "ulcerative colitis"[Title/Abstract]
)
AND
(
  "Machine Learning"[Mesh]
  OR "Artificial Intelligence"[Mesh]
  OR "machine learning"[Title/Abstract]
  OR "artificial intelligence"[Title/Abstract]
  OR "deep learning"[Title/Abstract]
  OR "neural network*"[Title/Abstract]
  OR "predictive model*"[Title/Abstract]
  OR "longitudinal"[Title/Abstract]
  OR "disease trajectory"[Title/Abstract]
  OR "disease progression"[Title/Abstract]
)
---

# 10. Build topic feeds with PubMed

This is where FreshRSS becomes much more useful than a simple journal list.

Instead of following only selected journals, use PubMed to discover papers from **any journal** matching a research topic.

A general workflow:

```text
Research question
      ↓
PubMed search
      ↓
Check results
      ↓
Create RSS
      ↓
Add RSS URL to FreshRSS
```

Example:

```text
"machine learning"[Title/Abstract]
AND
epidemiology[Title/Abstract]
```

Or:

```text
("inflammatory bowel disease"[Title/Abstract]
 OR Crohn*[Title/Abstract]
 OR "ulcerative colitis"[Title/Abstract])
AND
("machine learning"[Title/Abstract]
 OR "artificial intelligence"[Title/Abstract])
```

The exact searches should be tailored to the user's field.

## Good practice

Do not create dozens of broad searches immediately.

Start with perhaps:

- 3–5 high-value topic searches
- a small set of key journals

Then examine the article volume for a few days.

If a feed is too noisy, tighten the PubMed search.

---

# 11. Recommended research-feed strategy

A useful balance is:

```text
Research
├── Core Journals
│   ├── major general journals
│   ├── major specialty journals
│   └── important methods journals
│
└── PubMed
    ├── Topic 1
    ├── Topic 2
    ├── Topic 3
    └── Topic 4
```

### Direct journal feeds answer:

> What is appearing in the journals I care about?

### PubMed topic feeds answer:

> What is being published anywhere that matches my research interests?

Using both is usually much better than subscribing to every potentially relevant journal.

---

# 12. First update

After adding feeds, click FreshRSS's update/refresh button.

The first update can take a while.

Check:

- the main stream
- individual journal categories
- PubMed categories

---

# 13. Automatic updates

The Compose file contains:

```yaml
CRON_MIN: "0"
```

so FreshRSS updates automatically once per hour.

The computer and Docker Desktop must be running for updates to occur.

If the computer is shut down, FreshRSS will not fetch feeds during that period.

---

# 14. Common Docker commands

## Start

### Windows

```powershell
cd C:\FreshRSS
docker compose up -d
```

### macOS

```bash
cd ~/FreshRSS
docker compose up -d
```

## Stop

```text
docker compose down
```

This stops the container but does not delete the persistent data directory.

## Restart

```text
docker compose restart
```

## Check status

```text
docker compose ps
```

## View logs

```text
docker logs freshrss --tail 100
```

## Apply a Compose change

```text
docker compose up -d --force-recreate
```

---

# 15. Back up the installation

The most important directory is:

```text
data/
```

Also keep:

```text
compose.yml
extensions/
```

A simple backup is to copy the complete FreshRSS project directory.

Do not put the FreshRSS database or `data/` directory in a public Git repository.

---

# 16. Migrating to another computer

The setup is portable.

On the new computer:

1. Install Docker.
2. Create the project directory.
3. Copy:
   - `compose.yml`
   - `data/`
   - `extensions/`
4. Start:

```text
docker compose up -d
```

Open:

```text
http://localhost:8080
```

For a brand-new installation, use the OPML file instead of copying an existing database.

---

# 17. OPML is useful for sharing the feed collection

Once a good research collection has been built, export subscriptions from FreshRSS as OPML.

That OPML can be:

- backed up
- moved to another computer
- shared with another person
- used to recreate the subscription list

This is especially useful when several people want the same basic research-feed structure but different topic feeds.

---

# 18. Keep the installation simple at first

A good first version is:

```text
Docker
  ↓
FreshRSS
  ↓
10–20 direct journal feeds
  +
3–5 PubMed topic feeds
  ↓
hourly updates
```

Use it for several days before adding more.

The goal is to create a research feed with high signal, not to reproduce every new paper published.
