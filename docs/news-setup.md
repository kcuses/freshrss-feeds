# FreshRSS: Self-Hosted International, European and Danish News Feed

This guide shows how to build a personal news RSS reader with **FreshRSS + Docker**, using **two separate FreshRSS instances**:

- **News FreshRSS:** `http://localhost:8080`
- **Research FreshRSS:** `http://localhost:8081` (described separately in `research-setup.md`)

The News instance is intentionally separate from the Research instance. This keeps news subscriptions, categories, article state, and settings completely independent.

The setup works on **Windows, macOS, and Linux**.

---

# 1. What this setup does

```text
International RSS feeds ───┐
European RSS feeds ────────┤
Danish RSS feeds ──────────┤
Local RSS feed(s) ────────┤
                           ↓
                     FreshRSS News
                           ↓
                    localhost:8080
```

FreshRSS provides:

- one news reading interface
- categories
- unread/read state
- filtering
- search
- feed management
- local self-hosting
- automatic hourly updates

No paid hosting is required.

---

# 2. Requirements

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

WSL is not required on macOS.

## Linux

You need:

- a supported Linux distribution
- Docker Engine
- Docker Compose plugin

For Ubuntu/Debian, follow Docker's official Linux installation instructions:

https://docs.docker.com/engine/install/

Then verify:

```bash
docker --version
docker compose version
docker run hello-world
```

If Docker requires root privileges on your system, you may need to use `sudo` or configure the Docker user group according to Docker's documentation.

---

# 3. Install Docker

## Windows

Install Docker Desktop:

https://docs.docker.com/desktop/setup/install/windows-install/

Use the WSL 2 backend.

In PowerShell:

```powershell
wsl --install
```

Restart Windows when prompted.

Verify:

```powershell
wsl --status
wsl -l -v
```

The Linux distribution should use version 2.

Then verify Docker:

```powershell
docker --version
docker compose version
docker run hello-world
```

## macOS

Install Docker Desktop:

https://docs.docker.com/desktop/setup/install/mac-install/

Choose Apple Silicon or Intel as appropriate.

Start Docker Desktop.

In Terminal:

```bash
docker --version
docker compose version
docker run hello-world
```

WSL is not required on macOS.

## Linux

Install Docker Engine and the Docker Compose plugin using the official Docker documentation:

https://docs.docker.com/engine/install/

Then verify:

```bash
docker --version
docker compose version
docker run hello-world
```

---

# 4. Create the FreshRSS project directory

Use a directory dedicated to this project.

## Windows

```powershell
mkdir C:\FreshRSS
cd C:\FreshRSS
```

## macOS

```bash
mkdir -p ~/FreshRSS
cd ~/FreshRSS
```

## Linux

```bash
mkdir -p ~/FreshRSS
cd ~/FreshRSS
```

---

# 5. Create `compose.yml`

The two FreshRSS instances are defined in a single Docker Compose file.

## Windows

```powershell
notepad compose.yml
```

## macOS/Linux

```bash
nano compose.yml
```

Paste:

```yaml
services:
  freshrss-news:
    image: freshrss/freshrss:latest
    container_name: freshrss-news
    restart: unless-stopped
    ports:
      - "8080:80"
    environment:
      TZ: Europe/Copenhagen
      CRON_MIN: "0"
    volumes:
      - ./news-data:/var/www/FreshRSS/data
      - ./news-extensions:/var/www/FreshRSS/extensions

  freshrss-research:
    image: freshrss/freshrss:latest
    container_name: freshrss-research
    restart: unless-stopped
    ports:
      - "8081:80"
    environment:
      TZ: Europe/Copenhagen
      CRON_MIN: "0"
    volumes:
      - ./research-data:/var/www/FreshRSS/data
      - ./research-extensions:/var/www/FreshRSS/extensions
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
Europe/Stockholm
Europe/London
Europe/Berlin
America/Toronto
America/New_York
```

### Why there are two services

The instances are intentionally independent:

```text
News
  localhost:8080
  news-data/

Research
  localhost:8081
  research-data/
```

Each has its own database, subscriptions, categories, settings, and article state.

### Update frequency

Both services use:

```yaml
CRON_MIN: "0"
```

This runs the FreshRSS updater once per hour.

The News instance is intended for deliberate reading and catching up rather than replacing breaking-news notifications.

Save the file.

---

# 6. Start FreshRSS

From the project directory:

```text
docker compose up -d
```

Then verify:

```text
docker compose ps
```

You should see:

```text
freshrss-news
freshrss-research
```

both running.

Open the News instance:

```text
http://localhost:8080
```

---

# 7. Complete the News FreshRSS setup

FreshRSS will open its installation screen.

Choose:

- preferred interface language
- administrator username/password
- **SQLite** for the database

After setup, FreshRSS will open the News interface.

The Research instance has a completely separate setup at:

```text
http://localhost:8081
```

See `research-setup.md` for the Research installation.

---

# 8. Import the News OPML

Use the News OPML from this repository:

```text
opml/freshrss-news.opml
```

In FreshRSS:

**Subscription management → Import/Export → Import**

Select the News OPML.

After importing, click the refresh/update button.

The feed collection contains international, European, Danish, Nordic, UK, and other selected news sources.

---

# 9. Organize the news categories

A useful general structure is:

```text
Copenhagen
Denmark
Nordics
Europe
UK
Canada
United States
India
Global
```

A user can change this depending on their interests.

For example, someone living in Sweden could replace the Denmark section with:

```text
Stockholm
Sweden
Nordics
Europe
UK
...
```

The important idea is:

> **Geography is the primary organization of the news feed.**

---

# 10. International news

Start with a small set of strong international sources.

Examples:

- Reuters
- Associated Press
- BBC World
- Al Jazeera
- DW
- France 24

Do not subscribe to every possible international outlet.

A smaller set of high-quality sources usually produces a more usable feed.

---

# 11. European news

Use pan-European or international feeds rather than subscribing to every country individually.

Useful examples:

- BBC Europe
- DW
- France 24
- Euronews
- Politico Europe
- Reuters
- Associated Press
- Guardian World

This is particularly useful for users who want:

> important European events

rather than:

> a separate stream from every European country.

---

# 12. Danish news

For Denmark, a good starting point is:

### DR

DR has multiple news areas, including national and international news.

Useful categories can include:

- national news
- international news
- politics
- knowledge/science
- regional news

### Other Danish publishers

Depending on the user's preferences, add one or two newspapers such as:

- Politiken
- Berlingske
- Information

Do not feel that all three are necessary.

---

# 13. Local Danish news: easy solutions

Local news is the part that should be customized per user.

There are several easy options.

## Option A — regional public broadcaster

Use the local/regional feed from DR if available.

For example, a user in the Copenhagen area can use the DR Hovedstaden feed.

A user elsewhere in Denmark can choose the relevant DR regional area.

This is usually the easiest solution.

## Option B — local newspaper RSS

Search the local newspaper's website for:

```text
RSS
RSS feed
Subscribe
Feed
```

If a direct RSS feed exists, add it directly to FreshRSS.

## Option C — local public-service or government feeds

Local users can also look for RSS feeds from:

- municipalities
- police
- public agencies
- transport authorities
- universities

For example:

```text
Copenhagen Police RSS
```

can provide local public-safety updates.

## Option D — Google News RSS for a local query

When a site has no usable RSS feed, Google News can provide an RSS feed from a search.

For example:

```text
Copenhagen
```

or:

```text
Copenhagen OR København
```

can be used as a local search query.

This is a convenient fallback because the user doesn't have to build or host a scraper.

It is best used as a **supplement**, not as the only local-news source.

---

# 14. Adding feeds to FreshRSS

For every feed:

1. Go to **Subscription management**.
2. Click **Add**.
3. Paste the RSS/Atom URL.
4. Choose the relevant category.
5. Save.

For example:

```text
https://example.com/rss.xml
```

FreshRSS can generally consume either RSS or Atom feeds.

---

# 15. Prefer official feeds

Use this order of preference:

```text
Official publisher RSS
        ↓
Official Atom feed
        ↓
Public-service / government RSS
        ↓
Google News RSS
        ↓
Third-party feed conversion
```

Avoid building the core feed around unreliable third-party RSS conversion services when a first-party feed exists.

---

# 16. Building a balanced news collection

A good starting point might be:

```text
Denmark
    3–5 feeds

Copenhagen / local
    1–3 feeds

Nordics
    1–2 feeds

Europe
    5–8 feeds

UK
    2–3 feeds

Global
    4–6 feeds

United States
    3–5 feeds
```

Then adjust based on actual article volume.

The goal is not to maximize the number of subscriptions.

The goal is:

> enough independent sources to cross-check important events without creating hundreds of repetitive headlines.

---

# 17. Automatic hourly updates

Both FreshRSS instances contain:

```yaml
CRON_MIN: "0"
```

so both update automatically once per hour.

The computer and Docker must be running for updates to occur.

If the computer is shut down, FreshRSS will not fetch feeds during that period.

---

# 18. Useful Docker commands

These commands operate on both services unless you explicitly specify a service.

## Start

```text
docker compose up -d
```

## Stop

```text
docker compose down
```

This stops both containers but does not delete persistent data.

## Restart

```text
docker compose restart
```

## Check status

```text
docker compose ps
```

## View logs for both

```text
docker compose logs --tail 100
```

## View News logs

```text
docker logs freshrss-news --tail 100
```

## View Research logs

```text
docker logs freshrss-research --tail 100
```

## Apply Compose changes

```text
docker compose up -d --force-recreate
```

---

# 19. Backup

The two persistent data directories are:

```text
news-data/
research-data/
```

Extensions are stored in:

```text
news-extensions/
research-extensions/
```

A simple backup is to copy the complete FreshRSS project directory.

Do not put these runtime directories into a public Git repository.

---

# 20. Migrating News FreshRSS to another computer

The setup is portable.

On the new computer:

1. Install Docker.
2. Create the project directory.
3. Copy:
   - `compose.yml`
   - `news-data/`
   - `news-extensions/`
4. Start:

```text
docker compose up -d
```

Then open:

```text
http://localhost:8080
```

For a genuinely new News installation, use the News OPML instead of copying an existing database.

---

# 21. Migrating the complete setup

To move both News and Research together, copy:

```text
compose.yml
news-data/
news-extensions/
research-data/
research-extensions/
```

Then run:

```text
docker compose up -d
```

The two services will be restored at:

```text
http://localhost:8080
http://localhost:8081
```

---

# 22. Sharing a feed setup

Once a good collection has been created:

**FreshRSS → Subscription management → Export**

Export the subscriptions as OPML.

For this repository, keep News and Research exports separate:

```text
opml/freshrss-news.opml
opml/freshrss-research.opml
```

A recipient can import either collection into the corresponding FreshRSS instance and then add or remove local feeds as needed.

The combined OPML exists for completeness, but it is not part of the recommended two-instance setup.

---

# 23. Suggested GitHub repository structure

```text
freshrss-feeds/
├── README.md
├── docs/
│   ├── news-setup.md
│   ├── research-setup.md
│   └── sources.md
└── opml/
    ├── freshrss-news.opml
    ├── freshrss-research.opml
    └── freshrss-all.opml
```

Do not commit:

```text
news-data/
research-data/
```

or FreshRSS databases.

---

# 24. Local-news customization checklist

When a user wants local news:

- [ ] Check the regional public broadcaster.
- [ ] Check local newspaper RSS.
- [ ] Check local police/public-service RSS.
- [ ] Check municipal or public-agency feeds.
- [ ] Use a Google News RSS query if no direct RSS exists.
- [ ] Put local feeds in a dedicated category.
- [ ] Keep the local section small enough to remain useful.

---

# 25. Minimal recommended structure

For a general international + European + Danish setup:

```text
Copenhagen / Local
Denmark
Nordics
Europe
UK
Global
United States
```

Then add one or two local sources based on the user's actual location.

This gives a reusable base without assuming that every user wants the same national or local coverage.
