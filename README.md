# Trader Joe's Price Tracking (Python)

A Python port of [cmoog/traderjoes](https://github.com/cmoog/traderjoes). The original project is a Go/Nix implementation that powers [traderjoesprices.com](https://traderjoesprices.com) — this repo is just a Python rewrite of the price fetching and product search functionality.

## Features

- Search products by name
- Lookup by SKU
- Multi-store support
- SQLite storage for price tracking and history
- Concurrent fetching
- Smart cookie management with automatic Selenium fallback

## Installation

### Basic (with fallback cookies)
```bash
pip install requests
```

### Recommended (fully automatic cookie recovery)
```bash
pip install requests selenium webdriver-manager
```

Selenium enables automatic cookie retrieval — no manual cookie updates needed.

## Usage

### Search for Products

```bash
python3 traderjoes.py search "miso crunch"
python3 traderjoes.py search "pasta sauce"
```

### Lookup by SKU

```bash
python3 traderjoes.py lookup 073814 077316 060411
```

### Fetch All Store Data

```bash
# Fetch from default stores (Chicago, LA, NYC, Austin)
python3 traderjoes.py fetch

# Fetch from specific stores
python3 traderjoes.py fetch --stores 226 701 546
```

## Store Codes

- `226` - Default store in API
- `701` - Chicago South Loop
- `31` - Los Angeles
- `546` - NYC East Village
- `452` - Austin Seaholm

## Database

Creates `traderjoes.db` with this schema:

```sql
CREATE TABLE items (
    sku TEXT,
    retail_price TEXT,
    item_title TEXT,
    inserted_at TEXT,
    store_code TEXT,
    availability TEXT,
    item_description TEXT,
    sales_size TEXT,
    sales_uom_description TEXT,
    url_key TEXT
);
```

### Example Queries

```bash
sqlite3 traderjoes.db "SELECT item_title, retail_price FROM items WHERE item_title LIKE '%miso%';"
sqlite3 traderjoes.db "SELECT * FROM items ORDER BY inserted_at DESC LIMIT 10;"
```

## Cookie Management

The tool uses a smart cookie strategy:

1. Starts immediately with a fallback cookie
2. If a 403 occurs, automatically refreshes via Selenium
3. Retries the failed request seamlessly

You can also set a cookie manually:
```bash
export TJ_AFFINITY_COOKIE="your_cookie_value"
```

## Credits

Forked from [cmoog/traderjoes](https://github.com/cmoog/traderjoes). All credit for the original concept, API reverse-engineering, and [traderjoesprices.com](https://traderjoesprices.com) goes to the upstream project.
