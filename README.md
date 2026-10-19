# Backfilling dose-optimization design

Simulation functions for the proposed design and comparators.

## Files

- `run-bf-titeboin-efftox.R`: proposed design (`run.bfboin`), wrapper (`MAIN.func`), and a one-run example
- `competing_method.R`: TITE-BOIN-ET and TITE-BOIN12 (sourced by the main script)

BF-BOIN-ET is called via the `bfboinet` package (`get.oc.backboinet`).

## Example

From this directory:

```r
source("run-bf-titeboin-efftox.R")
```

The script runs **one** scenario under the paper-body setting (DLT window = 3 months, efficacy window = 3 months, accrual = 3 patients/month). Change `sc` (1–10) and `nsimu` at the bottom of the script. The paper used `nsimu = 5000`.
