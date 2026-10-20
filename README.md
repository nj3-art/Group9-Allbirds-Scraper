# Group 9 — Allbirds Web-Scraping Project

**Course:** Data Mining and Wrangling
**Target:** Allbirds public Shopify product feed — `https://www.allbirds.com/products.json`

## Group members

| # | Name | Registration No. |
|---|------|------------------|
| 1 | AGENORWOT CYNTHIA JILLIAN | S25B38/034 |
| 2 | NGOBI JONAH | S25B38/043 |
| 3 | JJUMBA JULIUS | M25B38/016 |
| 4 | AYOO ALICE | S25B38/026 |
| 5 | FLORA KEJI ABDU ABAS | S25B38/039 |

## Objectives

1. **Warm-up exercise.** Before working on the main target, we built a warm-up scraper (`Warm-up.ipynb`) to practise the basics of ethical scraping. It uses `requests` and `BeautifulSoup` to walk all 50 pages of the practice site `books.toscrape.com`, extracting each book's **title, price and rating** (1,000 books). It uses a 1-second polite delay between requests, a timeout and per-page error handling, and saves the result to `books_toscrape.csv`.
2. Inspect the target site and `robots.txt` first, and record the findings in the reconnaissance note (`RECONNAISSANCE NOTE.docx`).
3. Collect product and variant data (IDs, titles, product types, tags, sizes, prices, compare-at prices, availability and scrape timestamps) from the Allbirds JSON feed using Python HTTP requests, with pagination, retries, back-off and polite delays.
4. Keep the raw JSON and flattened CSV snapshots for every collection day, so that all cleaned output can be traced back to the collected data.
5. Clean the data and store it in SQLite (`products`, `variants`, `observations`).
6. Analyse how prices, discounts and availability change across the collection days, using eight charts with written interpretations.

## Project contents

| File / folder | Description |
|---|---|
| `Group9_Allbirds_Scraper.ipynb` | Main workflow: robots.txt check, paginated fetch, flattening, cleaning, SQLite loading, and the analysis charts |
| `Warm-up.ipynb` | Warm-up scraper for `books.toscrape.com` |
| `books_toscrape.csv` | Output of the warm-up (1,000 books: Title, Price, Rating) |
| `RECONNAISSANCE NOTE.docx` | Site structure, findings, choice of method, robots.txt and ethics |
| `allbirds.db` | SQLite database (`products`, `variants`, `observations`) |
| `raw/` | Dated raw JSON and flattened CSV snapshots, plus copies of the database and warm-up CSV |

## Method

- **Approach:** the Shopify JSON endpoint is called directly instead of parsing HTML. The data is already structured, needs fewer requests, and does not require browser automation.
- **Ethics:** `robots.txt` is checked before any collection, and scraping stops if the feed is disallowed. Requests use a descriptive User-Agent, a 2–5 second delay between requests, a daily request cap, and back-off on HTTP 403/429. We do not try to bypass blocks.
- **Scale:** each daily run retrieves **692 products** across three pages (250 per page) and produces **7,454 product-variant rows**.

## Collection days (13 – 19 October 2026)

The project covers **seven daily snapshots**, from 13 to 19 October 2026. Each has a raw JSON file and a flattened CSV in `raw/`:

| Date | Raw JSON | Flat CSV | How it was obtained |
|---|---|---|---|
| 2026-10-13 | `allbirds_raw_2026-10-13.json` | `allbirds_flat_2026-10-13.csv` | Scraped on the day |
| 2026-10-14 | `allbirds_raw_2026-10-14.json` | `allbirds_flat_2026-10-14.csv` | Scraped on the day |
| 2026-10-15 | `allbirds_raw_2026-10-15.json` | `allbirds_flat_2026-10-15.csv` | Scraped on the day |
| 2026-10-16 | `allbirds_raw_2026-10-16.json` | `allbirds_flat_2026-10-16.csv` | Scraped on the day |
| 2026-10-17 | `allbirds_raw_2026-10-17.json` | `allbirds_flat_2026-10-17.csv` | Scraped on the day |
| 2026-10-18 | `allbirds_raw_2026-10-18.json` | `allbirds_flat_2026-10-18.csv` | Scraped on the day |
| 2026-10-19 | `allbirds_raw_2026-10-19.json` | `allbirds_flat_2026-10-19.csv` | Scraped on the day |

### The 13th

We did not manage to run the scraper on 13 October. So that the seven-day series has no gap, the 13th was **filled in afterwards by copying the 14 October snapshot** (692 products, 7,454 variant rows, identical to the 14th). It is **not an independent scrape** of the site as it stood on the 13th. We are stating this openly because it affects how the day should be read:

- **Labelling.** Each row in the 13th's CSV carries an extra date-label column set to `2026-10-13`. Each object in the 13th's JSON carries extra fields giving the date label, the source collection date (`2026-10-14`) and a short note explaining where the data came from.
- **Timestamps are not altered.** The original `scraped_at` (for example `2026-10-14T09:15:48+00:00`) and the products' `updated_at` values are kept as they were. The embedded timestamps therefore do not claim a collection earlier than the 14th.
- **Interpretation.** For the 13th, prices, discounts and stock levels are those observed on the 14th, so the 13th → 14th step shows **no change by construction**. Any trend or change measured across 13–14 October should not be read as real movement, and the genuinely independent observations are the six days from 14 to 19 October. The SQLite database (`allbirds.db`) holds observations for 16–19 October only; the full 13–19 series lives in the CSV and JSON snapshots, which the analysis cells in the notebook read from `raw/`.

## Reproducing the results

1. Open `Group9_Allbirds_Scraper.ipynb` in Jupyter.
2. Run the cells in order. The robots.txt check runs first, and the offline self-test confirms parsing, cleaning and loading before the live run.
3. The daily-run cell saves the dated raw JSON and flattened CSV into `raw/` and loads the day's observations into `allbirds.db`.
4. Run the final analysis cell, which reads every `raw/allbirds_flat_*.csv`, to produce the eight charts and their interpretations.

Requirements: Python 3, `requests`, `pandas`, `matplotlib` (and `beautifulsoup4` for the warm-up).

## Notes

- The public feed and its availability can change over time; blocked requests or errors should be recorded, not bypassed.
- Each snapshot records the state of the site at the time it was run. A past day cannot be reconstructed after the fact, which is why the 13th is documented as above.
