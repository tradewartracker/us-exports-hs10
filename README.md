# us-exports-hs10

US export trade data at the HS-10 commodity code level, sourced from the [US Census Bureau International Trade API](https://www.census.gov/data/developers/data-sets/international-trade.html). Covers the top ~33 trading partners plus aggregate totals, from 2013-01 through the most recently downloaded month.

## Repository structure

```
make-exports-hs10-dataset.ipynb    # Step 1: download new monthly data from Census API
combine-exports-hs10-dataset.ipynb # Step 2: append new months and rebuild final output
data/
  exports-hs10/
    {COUNTRY_CODE}data-{YYYY-MM}.parquet   # one file per country per new month downloaded
    {COUNTRY_CODE}data-combined.parquet    # full history (2013-01 onward) per country
    All-country-exports.parquet            # all countries combined (final output)
```

## Country codes

Files are named by Census Bureau country codes. Key codes:
- `TOTAL` — total for all countries
- `0003` — NAFTA / USMCA aggregate
- `0020` — European Union aggregate
- All other codes are individual country codes (e.g. `4700` = Canada, `2010` = China)

## Data columns

Each parquet file contains columns pulled directly from the Census API:
- `E_COMMODITY` — 10-digit HS commodity code
- `E_COMMODITY_SDESC` — short description of the commodity
- `ALL_VAL_MO` — export value for the month (USD)
- `CTY_NAME` — country name
- `time` — year-month (YYYY-MM)

## Updating with new monthly data

The notebooks do **not** need all historical monthly files on disk — only the per-country combined files are required. To add a new month:

1. **`make-exports-hs10-dataset.ipynb`** — Set `current_date` to the new month (e.g. `"2026-02"`) and update `start` in the download cell to match. Run all cells. This downloads one `{COUNTRY}data-{YYYY-MM}.parquet` file per country into `data/exports-hs10/`.

2. **`combine-exports-hs10-dataset.ipynb`** — Set `current_date` to the same new month. Run all cells. The append cell loads each existing `*data-combined.parquet`, appends the new monthly file, and overwrites in place. The final cell rebuilds `All-country-exports.parquet`.

> **Note:** The commented-out cell in `combine-exports-hs10-dataset.ipynb` (cell 3) was used to build the initial combined files from individual monthly files and does not need to be run again under normal circumstances.

## Source

US Census Bureau — International Trade in Goods, HS commodity classification, exports endpoint:
`https://api.census.gov/data/timeseries/intltrade/exports/hs`

