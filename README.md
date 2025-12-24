# JLCPCB-Basic-Parts-Table-Generator
<img width="1214" height="717" alt="image" src="https://github.com/user-attachments/assets/75e3b695-82d1-4eeb-9a15-fbe0609e6169" />

Scrapes the **JLCPCB Basic Parts** list from the UI, paginates through all pages, collects part codes and detail links, then opens each part detail page to extract enriched fields and exports everything to a single Excel file.

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
- `jlcpcb_basic_parts_with_links_enriched.xlsx`

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
