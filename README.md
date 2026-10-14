# Refining Fundamentals → Refiner Equity Returns

*A walk-forward nowcast with a Markov regime filter, tested on 7 US refiners. Built in R.*

## Question

Can monthly refining fundamentals (crack spreads, crude differentials, product prices, inventories and demand)
explain a refiner's stock return, and can the gap between the actual return and that fundamental "fair" return be traded?

## Method

1. **Nowcast.** Each month, ~930 candidate variables are scored on significance, stability of fit and their own
   out-of-sample R². The top-ranked variables, with a cap on their correlation with each other, form a nowcast of
   the stock's monthly return.
2. **Walk-forward calibration.** Windows, number of variables and score weights are re-calibrated every month on
   past data only (SPSA optimiser), so no future information enters any decision.
3. **Trading the residual.** When the actual return deviates from the nowcast by more than a calibrated threshold,
   the model trades for reversion. Take-profit and stop-loss sit at 1× the residual, holding is at most 6 months, and
   costs are 10 bps per side.
4. **Regime filter.** A 3-state hidden Markov model, fitted monthly on past data only, decides when the signal
   should be traded as is and when it should be reversed. "Graded" uses an ensemble of fits over 12, 24 and 36-month
   windows.

## Results (net of costs)

**Valero (VLO)**, the stock the model was built on, trading 2014–2026:

| | Sharpe | Max drawdown |
|---|---|---|
| Baseline (no regime filter) | 0.14 – 0.26 | ~ -15% |
| With regime filter (4 random seeds) | **0.51 – 0.74** | ~ -14 to -18% |

**Peer refiners.** Same settings, each stock calibrated on its own data, regime rule "graded":

| Stock | Data from | Baseline Sharpe | With regime filter |
|---|---|---|---|
| DINO | 2006 | -0.54 | 0.49 |
| DK | 2006 | -0.23 | 0.07 |
| CVI | 2007 | -0.02 | 0.11 |
| MPC | 2011 | 0.06 | 0.59 |
| PBF | 2012 | -0.45 | 0.21 |
| PSX | 2012 | -0.01 | -0.20 |
| **Average** | | **-0.20** | **0.21** |

![VLO: baseline vs regime filter](results/20_base_markov_flip.png)

![Sharpe by stock](results/30_all_sharpe.png)

![Return by calendar year](results/34_all_years.png)

## What I learned

- **The raw fundamental signal is VLO-specific.** On peers it was flat to negative. For DINO and PBF the residual was
  wrong most of the time (hit rates 36–43%): mispricings tended to continue rather than revert.
- **The regime filter is the part that generalises.** It improved 5 of 6 peers, mainly by recognising periods when
  the signal should be traded in reverse, and cut drawdowns. PSX was the exception.
- **History length helps, but it is not the whole story.** Re-running VLO with only post-2012 data roughly halved its
  Sharpe. However, DK and CVI have as much history as VLO and still showed a weak raw signal.
- **Caveats.** The regime rule "graded" was chosen after comparing four rules. Results vary by about ±0.1–0.2 Sharpe
  between random seeds. Buy-and-hold had a higher Sharpe over this period, but with drawdowns of 65–79%. This is a
  research project, not a trading recommendation.

## Repository

| Path | Content |
|---|---|
| `Mean_Reversion_Model.Rmd` | The full model, from data loading to backtest, regime filter, all-stock comparison and charts |
| `data/README.md` | Expected input files and the variable groups (data not included: licensed) |
| `results/` | Summary tables and charts |

To run it, put the three input files in `data/`, open the `.Rmd` in RStudio and click **Knit**.
`QUICK_MODE = TRUE` takes about 10 minutes per stock on a laptop.
