# Forecast data for "<PAPER TITLE>"

Companion data for:

> Puphaiboon, K., et al. <PAPER TITLE>. Submitted to *XXX*, 2026. <DOI when available>

The paper studies PPO fine-tuning of a forecast-driven knapsack allocation
for ERCOT day-ahead / real-time virtual bidding at five hubs. This
repository releases the hourly price forecasts that every arm in the paper
consumes, so that readers can check them against public ERCOT prices.

## What is released

| Folder | Paper name | Contents |
|---|---|---|
| `forecasts/f_int_external/` | F-int (internal forecast) | Hourly DA and RT price forecasts, 5 hubs. Used for every result in the main text. |
| `forecasts/f_pub_lightgbm/` | F-pub (public-input forecast) | Hourly DA and RT price forecasts, 5 hubs, from the LightGBM forecaster of Appendix "Public-Input Forecast Control". |

Hubs: HOUSTON, NORTH, SOUTH, WEST, PANHANDLE (ERCOT hub settlement points).

## What is not released

- The code and model of F-int. It was produced by a separate team and
  is described in the paper only as a black box.
- The trading environment, allocation and PPO code.
- Realised ERCOT prices. These are public; see "Realised prices" below.

## File formats

All files are comma-separated, one row per hour, timestamps in ERCOT
local time (US Central), hour-beginning.

### F-int: `forecasts/f_int_external/HB_<HUB>_en_update.csv`

| Column | Meaning |
|---|---|
| `price_dam_pred` | Forecast DA settlement-point price, $/MWh |
| `price_rtm_pred` | Forecast RT settlement-point price (hourly mean), $/MWh |
| <!-- FILL: timestamp column and any other columns --> | |

Spread forecast used in the paper: `price_rtm_pred - price_dam_pred`.

Coverage: January 2024 to July 2026. 47 hours of the out-of-sample window
carry no forecast; the paper excludes those hours from every arm.

Provenance: the paper's out-of-sample results assume that the forecast
for operating day d used no realised price of day d or later. This was
verified by code review of the forecasting pipeline and written
attestation from its authors only. The forecasts cannot be regenerated
from this repository, and every F-int result is conditional on that
attestation (paper, Section "Forecasts and their provenance").

### F-pub: `forecasts/f_pub_lightgbm/fpub_forecast_<HUB>.csv`

| Column | Meaning |
|---|---|
| `DATETIME` | Hour-beginning timestamp, `YYYY-MM-DD HH:MM:SS` |
| `DA_PRED` | Forecast DA settlement-point price, $/MWh |
| `RT_PRED` | Forecast RT settlement-point price (hourly mean), $/MWh |
| `SPREAD_PRED` | `RT_PRED - DA_PRED`, the spread forecast used in the paper |

Coverage: 2023-10-01 00:00 to 2026-07-16 19:00, 24,431 rows per hub. The
first rows begin after the forecaster's 180-day minimum training history.
Out-of-sample window used in the paper: 2025-07-01 to 2026-07-16, 9,097
rows per hub, no missing values.

The forecaster is refitted at the start of each calendar quarter on all
earlier data, so every row is a genuine pre-deadline forecast. The model
and its inputs are specified in the paper's appendix.

## Splits used in the paper

| Split | Dates |
|---|---|
| Training | 2024-01 to 2025-06 |
| Validation (checkpoint selection only) | 2024-11-02 to 2025-03-13 |
| Recalibration fold | 2025-03-13 to 2025-06-30 |
| Out-of-sample test | 2025-07-01 to 2026-07-16 (380 operating days) |

## Realised prices

Realised DA and RT settlement-point prices are public from ERCOT
(https://www.ercot.com):

- DA: report NP4-190 (DAM Settlement Point Prices)
- RT: report NP6-905 (Settlement Point Prices at Resource Nodes, Hubs and
  Load Zones), 15-minute intervals. The paper uses their hourly mean.

The realised spread is RT - DA. A positive value means a DEC (buy DA,
sell RT) position earns money.


## Licence

CC-BY-4.0 for the data

## Citation

If you use these data, please cite the paper above.

## Contact

Kreecha Puphaiboon, Banpu Public Company Limited.

kreecha.p@banpu.co.th