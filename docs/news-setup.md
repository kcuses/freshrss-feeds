# FreshRSS: Self-Hosted International, European and Danish News Feed

This guide shows how to build a personal news RSS reader with **FreshRSS + Docker**, running locally on either **Windows or macOS**.

The setup is intended for:

- international/world news
- European news
- Danish news
- optional local/regional news

The local component is deliberately flexible so that the same base setup can work for users in different Danish cities, regions, or countries.

No paid hosting is required.

---

# 1. What this setup does

```text
International RSS feeds ───┐
European RSS feeds ────────┤
Danish RSS feeds ──────────┤
Local RSS feed(s) ────────┤
                           ↓
                       FreshRSS
                           ↓
                  http://localhost:8080
```

FreshRSS provides:

- one reading interface
- categories
- unread/read state
- filtering
- search
- feed management
- local self-hosting
- automatic updates

---

# 2. Install Docker

## Windows

Windows Home is sufficient when using **Linux containers**.

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

---

# 3. Create the FreshRSS project directory

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

---

# 4. Create `compose.yml`

## Windows

```powershell
notepad compose.yml
```

## macOS

```bash
nano compose.yml
```

Paste:

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

Replace the timezone if needed.

Examples:

```text
Europe/Copenhagen
Europe/Riga
Europe/Stockholm
Europe/London
```

The example uses:

```yaml
CRON_MIN: "0"
```

which means FreshRSS updates once per hour.

---

# 5. Start FreshRSS

From the project directory:

```text
docker compose up -d
```

Check:

```text
docker compose ps
```

Open:

```text
http://localhost:8080
```

---

# 6. Complete the initial FreshRSS setup

On the FreshRSS setup screen:

- choose the language
- create the administrator account
- use **SQLite** for a simple personal installation

After setup, FreshRSS will open the main interface.

---

# 7. Organize the news categories

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

# 8. International news

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

# 9. European news

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

# 10. Danish news

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

# 11. Local Danish news: easy solutions

Local news is the part that should be customized per user.

There are several easy options.

## Option A — regional public broadcaster

Use the local/regional feed from DR if available.

For example, a user in the Copenhagen area can use the DR Hovedstaden feed.

A user elsewhere in Denmark can choose the relevant DR regional area.

This is usually the easiest solution.

---

## Option B — local newspaper RSS

Search the local newspaper's website for:

```text
RSS
RSS feed
Subscribe
Feed
```

If a direct RSS feed exists, add it directly to FreshRSS.

---

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

This can be particularly useful if local newspapers have poor RSS support.

---

## Option D — Google News RSS for a local query

When a site has no usable RSS feed, Google News can provide an RSS feed from a search.

For example, a user interested in Copenhagen could create a query for:

```text
Copenhagen
```

or:

```text
Copenhagen OR København
```

and then use the generated Google News RSS URL.

This is a convenient fallback because the user doesn't have to build or host a scraper.

It is best used as a **supplement**, not as the only local-news source.

---

# 12. Adding feeds to FreshRSS

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

# 13. Prefer official feeds

Use this order of preference:

```text
Official publisher RSS
        ↓
Official Atom feed
        ↓
Pubic-service / government RSS
        ↓
Google News RSS
        ↓
Third-party feed conversion
```

Avoid building the core feed around unreliable third-party RSS conversion services when a first-party feed exists.

---

# 14. Building a balanced news collection

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

# 15. Automatic hourly updates

The Compose file uses:

```yaml
CRON_MIN: "0"
```

FreshRSS will update feeds once per hour.

This is a good default for a personal news reader that is **not intended to replace breaking-news alerts**.

Urgent notifications can continue to come from:

- phone news apps
- publisher apps
- operating-system notifications

FreshRSS can then be used as the place for deliberate reading and catching up.

---

# 16. Useful Docker commands

## Start

```text
docker compose up -d
```

## Stop

```text
docker compose down
```

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

## Apply Compose changes

```text
docker compose up -d --force-recreate
```

---

# 17. Backup

Keep backups of:

```text
compose.yml
data/
extensions/
```

The most important persistent data is in:

```text
data/
```

Do not commit FreshRSS's `data/` directory to a public GitHub repository.

---

# 18. Sharing a feed setup

Once a good collection has been created:

**FreshRSS → Subscription management → Export**

Export the subscriptions as OPML.

The OPML can then be:

- shared with another user
- imported on another computer
- stored in GitHub
- used as a baseline for a second installation

A recipient can then delete or add local feeds after importing.

---

# 19. Suggested GitHub repository structure

For a public reusable setup:

```text
freshrss-news/
├── README.md
├── docs/
│   └── FreshRSS_News_Setup_Guide.md
└── opml/
    └── news_base.opml
```

For a repository containing multiple variants:

```text
freshrss-feeds/
├── README.md
├── docs/
│   ├── research-guide.md
│   └── news-guide.md
└── opml/
    ├── research.opml
    └── news-international-europe-denmark.opml
```

Do not commit:

```text
data/
```

or FreshRSS databases.

---

# 20. Local-news customization checklist

When a user wants local news:

- [ ] Check the regional public broadcaster.
- [ ] Check local newspaper RSS.
- [ ] Check local police/public-service RSS.
- [ ] Check municipal or public-agency feeds.
- [ ] Use a Google News RSS query if no direct feed exists.
- [ ] Put local feeds in a dedicated category.
- [ ] Keep the local section small enough to remain useful.

---

# 21. Minimal recommended structure

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
