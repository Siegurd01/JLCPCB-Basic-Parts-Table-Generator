# JLCPCB Basic Parts Table Generator
<img width="1222" height="915" alt="image" src="https://github.com/user-attachments/assets/0663a0b8-a86d-4f2f-babf-04956d6c3abb" />

Single file python script that scrapes the **JLCPCB Basic Parts** list from the UI, paginates through all pages, collects part codes and detail links, then opens each part detail page to extract enriched fields and exports everything to a single Excel file.

## What it does

1. Opens the JLCPCB Basic Parts list page.
2. Ticks **Parts Type → Basic**, clicks **Apply**.
3. Paginates through the list and builds a unique set of **(Code, Link)**.
4. Visits each part detail page (1 navigation per part) and extracts:
   - `Package`
   - `Description`
   - `In Stock` (parsed from `In Stock: ####`)
   - `CategoryPath` (breadcrumb path)
   - `Type` (last 1–2 breadcrumb categories)
   - `DatasheetURL` (DOM/HTML extraction + optional download-event fallback)
5. Exports to Excel with hyperlink cells.

## Output
<img width="1624" height="322" alt="image" src="https://github.com/user-attachments/assets/c21b9b28-453c-45ec-889f-ede7d73f656d" />

Default output file:
- `jlcpcb_basic_parts_2025-12-24.xlsx`

Columns:
- `Code`
- `Type`
- `Package`
- `In Stock`
- `Description`
- `Link`
- `Datasheet`
- `_detail_error` (if any)
- `CategoryPath`

## Requirements

- Python 3.8+ (recommended 3.10+)
- Playwright (Python)
- openpyxl

## Installation example (recommended: Python venv)

### Windows (PowerShell)

```powershell
$ErrorActionPreference = "Stop"

mkdir C:\JLCPCB_basic_parts_list -Force | Out-Null
cd C:\JLCPCB_basic_parts_list

git clone https://github.com/Siegurd01/JLCPCB-Basic-Parts-Table-Generator repo
cd repo

python -m venv .venv
Set-ExecutionPolicy -Scope CurrentUser RemoteSigned -Force
.\.venv\Scripts\Activate.ps1

python -m pip install --upgrade pip
pip install -r requirements.txt

python -m playwright install chromium

python .\scrape_basic_parts_playwright.py
```
### Linux (bash)
```bash
set -e

mkdir -p "$HOME/JLCPCB_basic_parts_list"
cd "$HOME/JLCPCB_basic_parts_list"

git clone https://github.com/Siegurd01/JLCPCB-Basic-Parts-Table-Generator repo
cd repo

python3 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip
pip install -r requirements.txt

python -m playwright install chromium

python ./scrape_basic_parts_playwright.py
```
### macOS (zsh/bash)
```zsh
set -e

mkdir -p "$HOME/JLCPCB_basic_parts_list"
cd "$HOME/JLCPCB_basic_parts_list"

git clone https://github.com/Siegurd01/JLCPCB-Basic-Parts-Table-Generator repo
cd repo

python3 -m venv .venv
source .venv/bin/activate

python -m pip install --upgrade pip
pip install -r requirements.txt

python -m playwright install chromium

python ./scrape_basic_parts_playwright.py
```
## Why this scraper intentionally runs slowly (~4s per component)

This project visits hundreds of part-detail pages. Adding a small delay between requests is important for both reliability and responsible use:

- **To respect server rate limits and avoid 429 errors.** Many sites apply rate limiting and will respond with **HTTP 429 (Too Many Requests)** when a client sends too many requests in a short time. Slowing down reduces the chance of being throttled or blocked.

- **To avoid overloading the website.** “Polite scraping” practices recommend controlling request rate so you don’t create unnecessary load or degrade the site for normal users. 

- **To reduce the risk of IP bans / anti-bot triggers.** Aggressive crawling can trigger alarms, incident response, or even outages; high-volume scraping has been publicly documented as causing operational issues and drawing attention from site operators. :contentReference[oaicite:2]{index=2}

- **To align with crawler guidance (where applicable).** Site owners can publish crawling preferences (e.g., via `robots.txt` and directives such as `Crawl-delay` for some crawlers). Even when not universally supported, the underlying intent is to prevent overwhelming servers.

- **To improve data completeness on dynamic pages.** Part detail pages often render content asynchronously (package, breadcrumb/category path, datasheet link, stock). A brief wait helps ensure fields are actually present before extraction, reducing empty columns and retries.
