# FreshRSS: Self-Hosted Research Article Feed

This guide shows how to build a personal research-article RSS reader with **FreshRSS + Docker**, using **two separate FreshRSS instances**:

- **Research FreshRSS:** `http://localhost:8081`
- **News FreshRSS:** `http://localhost:8080` (described separately in `news-setup.md`)

The research instance is intentionally separate from the news instance. This keeps research subscriptions, categories, article state, and settings completely independent.

The setup works on **Windows, macOS, and Linux**.

---

# 1. What this setup does

```text
Journal RSS feeds ──────────────┐
                                │
PubMed topic RSS feeds ────────┤
                                ↓
                     FreshRSS Research
                                ↓
                       localhost:8081
```

FreshRSS becomes a dedicated research dashboard containing:

- direct feeds from journals that provide RSS/Atom
- PubMed RSS feeds for topics or journals
- categories for organizing journals
- a local, self-hosted reader
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

Use the WSL 2 backend and Linux containers.

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

Your Linux distribution should use version 2.

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
Europe/London
Europe/Berlin
America/Toronto
America/New_York
Australia/Sydney
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

For a personal research reader, hourly updates are a good starting point.

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

Open the Research instance:

```text
http://localhost:8081
```

---

# 7. Complete the Research FreshRSS setup

FreshRSS will open its installation screen.

Choose:

- preferred interface language
- administrator username/password
- **SQLite** for the database

After setup, FreshRSS will open the Research interface.

The News instance has a completely separate setup at:

```text
http://localhost:8080
```

See `news-setup.md` for the News installation.

---

# 8. Import the Research OPML

Use the Research OPML from this repository:

```text
opml/freshrss-research.opml
```

In FreshRSS:

**Subscription management → Import/Export → Import**

Select the Research OPML.

After importing, click the refresh/update button.

The feed collection contains both direct journal feeds and PubMed-derived feeds.

---

# 9. Build your research categories

A useful general structure is:

```text
Nature
Cell
Science
Lancet & Elsevier
BMJ
PLOS
Springer
PubMed
```

Alternatively, users can create broader categories such as:

```text
Core Journals
Methods
Clinical / Medicine
Population Health
Biology / Molecular
PubMed
```

The important point is to avoid making too many folders at the beginning.

---

# 10. Add journal RSS feeds

For journals that provide a publisher RSS or Atom feed:

1. Open the journal website.
2. Look for:
   - RSS
   - RSS feed
   - Subscribe
   - Feed
   - Alerts
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

# 11. Use PubMed when a journal has no reliable RSS

PubMed is an excellent fallback because it can create RSS feeds from searches.

Go to:

https://pubmed.ncbi.nlm.nih.gov/

Run a search, then use **Create RSS**.

This is useful when:

- the publisher has no public RSS
- the publisher's RSS endpoint is broken
- the journal has moved platforms
- you want to monitor a research topic across multiple journals

The Research OPML in this repository already contains four pre-configured PubMed RSS feeds. The searches used to create them are documented in this section so that they can be inspected, modified, or regenerated if necessary.

## PubMed IBD and Computational Methods

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
```

The generated RSS feed is included in the Research OPML as:

`PubMed IBD and Computational Methods`

## PubMed Epidemiology and ML

```text
(
  "Epidemiology"[Mesh]
  OR epidemiolog*[Title/Abstract]
  OR "population health"[Title/Abstract]
  OR "life course"[Title/Abstract]
  OR "life-course"[Title/Abstract]
)
AND
(
  "Machine Learning"[Mesh]
  OR "Artificial Intelligence"[Mesh]
  OR "machine learning"[Title/Abstract]
  OR "artificial intelligence"[Title/Abstract]
  OR "deep learning"[Title/Abstract]
  OR "predictive model*"[Title/Abstract]
  OR "representation learning"[Title/Abstract]
  OR longitudinal[Title/Abstract]
  OR "survival analysis"[Title/Abstract]
  OR "time-to-event"[Title/Abstract]
)
```

The generated RSS feed is included in the Research OPML as:

`PubMed Epidemiology and ML`

## PubMed Multi-omics and Biomarkers

```text
(
  "Multi-Omics"[Title/Abstract]
  OR "multiomics"[Title/Abstract]
  OR "multi omics"[Title/Abstract]
  OR "proteomics"[Mesh]
  OR proteomic*[Title/Abstract]
  OR "metabolomics"[Mesh]
  OR metabolomic*[Title/Abstract]
  OR "transcriptomics"[Title/Abstract]
  OR "genomics"[Mesh]
  OR genomics[Title/Abstract]
  OR "blood biomarkers"[Title/Abstract]
  OR biomarker*[Title/Abstract]
  OR multimodal[Title/Abstract]
  OR "multi-modal"[Title/Abstract]
)
AND
(
  human*[Title/Abstract]
  OR patient*[Title/Abstract]
  OR clinical[Title/Abstract]
  OR disease[Title/Abstract]
  OR health[Title/Abstract]
)
```

The generated RSS feed is included in the Research OPML as:

`PubMed Multi-omics and Biomarkers`

## PubMed Causal and Population Health

```text
(
  "Causal Inference"[Title/Abstract]
  OR "causal inference"[Title/Abstract]
  OR "target trial"[Title/Abstract]
  OR "target trial emulation"[Title/Abstract]
  OR "Mendelian randomization"[Title/Abstract]
  OR "Mendelian randomisation"[Title/Abstract]
  OR "health inequalities"[Title/Abstract]
  OR "health inequities"[Title/Abstract]
  OR "environmental epidemiology"[Title/Abstract]
  OR "environmental exposure"[Title/Abstract]
  OR "air pollution"[Title/Abstract]
  OR climate[Title/Abstract]
)
AND
(
  epidemiolog*[Title/Abstract]
  OR population[Title/Abstract]
  OR health[Title/Abstract]
  OR disease[Title/Abstract]
  OR mortality[Title/Abstract]
  OR "public health"[Title/Abstract]
)
```

The generated RSS feed is included in the Research OPML as:

`PubMed Causal and Population Health`

### Recreating or modifying the feeds

The RSS URLs generated by PubMed contain a PubMed-generated search identifier. The **search queries above are the reproducible part**.

To recreate a feed:

1. Copy the desired query.
2. Paste it into PubMed.
3. Run the search.
4. Select **Create RSS**.
5. Choose a suitable number of results.
6. Add the generated RSS URL to FreshRSS.

---

# 12. Recommended research-feed strategy

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

# 13. First update

After adding/importing feeds, click FreshRSS's update/refresh button.

The first update can take a while.

Check:

- the main stream
- individual journal categories
- PubMed categories

---

# 14. Automatic updates

Both FreshRSS instances contain:

```yaml
CRON_MIN: "0"
```

so both update automatically once per hour.

The computer and Docker must be running for updates to occur.

If the computer is shut down, FreshRSS will not fetch feeds during that period.

---

# 15. Useful Docker commands

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

# 16. Backup

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

# 17. Migrating Research FreshRSS to another computer

The setup is portable.

On the new computer:

1. Install Docker.
2. Create the project directory.
3. Copy:
   - `compose.yml`
   - `research-data/`
   - `research-extensions/`
4. Start:

```text
docker compose up -d
```

Then open:

```text
http://localhost:8081
```

For a genuinely new Research installation, use the Research OPML instead of copying an existing database.

---

# 18. Keep the installation simple at first

A good first version is:

```text
Docker
  ↓
FreshRSS Research
  ↓
10–20 direct journal feeds
  +
3–5 PubMed topic feeds
  ↓
hourly updates
```

Use it for several days before adding more.

The goal is to create a research feed with high signal, not to reproduce every new paper published.
