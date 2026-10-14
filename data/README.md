# Data

The input files are **not included**: they are built from licensed market data that cannot be redistributed.
To run the model, put three Excel files in this folder with the layout below.

## `fundamentals_monthly.xlsx` (first sheet)

| Column | Content |
|---|---|
| 1 | Month, e.g. `2006 - 1` or a date |
| 2 | Main stock's (VLO) monthly return |
| 3 … | One column per candidate variable, ~930 in total |

Candidate variables, each with lags 0–6 months as separate columns (`NAME -1` … `NAME -6`):

- **Crack spreads:** levels (CRCK1–24), monthly changes, and deviations from 3–6-month averages
- **Crude differentials:** spreads between benchmark and regional crude grades (WTI, Brent, Dubai, LLS, Mars, Urals and others)
- **Product prices and spreads:** gasoline, diesel / gasoil, jet, naphtha and fuel oil spreads; product price changes and deviations
- **Freight:** shipping-rate ratios between routes
- **US inventories and demand:** monthly changes and deviations for gasoline, distillate, jet, propane and total products
- **Refinery utilisation and margin indices**
- **The stock's own return history:** `StockReturns -k`, `AVGRETk` (rebuilt automatically from daily returns for peers)

## `main_stock_daily.xlsx` (first sheet)

Column 1 = date, column 2 = the main stock's daily return (decimal, e.g. 0.012 = +1.2%).

## `peers_daily.xlsx` (sheet `DailyChanges`)

Column `Date`, then one column of daily returns per peer company. Column names must match `PEER_COLS` in the model,
e.g. `HFSinclair` (DINO), `Delek` (DK), `CvrEnerg` (CVI), `Marathon` (MPC), `PBFEnergy` (PBF), `Philips66` (PSX).
Cells before a company's listing date stay empty.
