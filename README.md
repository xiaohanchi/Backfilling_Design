# Backfilling dose-optimization design

R code for a Bayesian phase I/II design with backfilling for dose optimization under late-onset toxicity and efficacy. The ready-to-use function is **`run.bfboin()`** in `run_design.R`.

## Setup

Required packages: `dplyr`, `BOIN`, `boinet`, `bfboinet`. From this directory:

```r
source("run_design.R")
```

This loads `run.bfboin()` (proposed design) and, for optional comparisons, the wrappers in `competing_method.R`.

## How to run the proposed design

Call `run.bfboin()` with true toxicity/efficacy probabilities and the trial setting. The example below matches the paper-body configuration (target DLT rate 0.25, assessment windows of 3 months, accrual of 3 patients per month, utility scores \((u_1, u_2, u_3, u_4) = (100, 40, 60, 0)\), extended backfilling, and utility-based backfilling using posterior probability).

```r
result <- run.bfboin(
  target        = 0.25,
  pT.true       = c(0.15, 0.25, 0.35, 0.40, 0.45),  # true DLT rates
  pE.true       = c(0.35, 0.35, 0.36, 0.37, 0.37),  # true efficacy rates
  w0            = 2 / 3,      # utility: (100, 40, 60, 0)
  ncohort       = 10,
  cohortsize    = 3,
  accuralrate   = 3,          # patients per month
  DLTwindow     = 3,          # months
  effwindow     = 3,          # months
  n.earlystop   = 12,
  n.backfilling = 12,
  bf.type       = "utility",
  bf.extended   = TRUE,
  utility.method = "pp",
  pE.low        = 0.20,
  cutoff.eff    = 0.90,
  min.complete  = c(tox = 0.51, eff = 0.31),
  min.MF        = c(tox = 0.25, eff = 0),
  ntrial        = 1000,       # paper used 5000
  seed          = 123
)
```

Change `pT.true` / `pE.true` for another scenario, and `ntrial` for the number of simulated trials.

## How to read the output

`run.bfboin()` returns a list. The main operating characteristics are:

| Object | Meaning |
|--------|---------|
| `result$sel_MTD` | MTD selection percentage at each dose |
| `result$sel_OBD` | OBD selection percentage at each dose |
| `result$percentstop` | Percentage of trials that stop early (no MTD) |
| `result$npatients` | Average number of patients per dose; rows are dose-finding (`EN.s1`), extended backfilling (`EN.s2`), and `total` |
| `result$totaln` | Average sample size: dose-finding (`s1`), extended backfilling (`s2`), and `total` |
| `result$ntox` / `result$totaltox` | Average number of DLTs by dose / overall |
| `result$duration` | Mean trial duration (months): `dose_finding` and `extended_bf` (total duration with extended backfilling) |

Example:

```r
result$sel_MTD      # e.g. 45.8  32.1  ...  → % selecting dose 1, 2, ...
result$sel_OBD
result$npatients    # average patients at each dose
result$totaln       # average total sample size
result$duration     # average duration in months
```

## Optional: compare with other designs

`MAIN.func()` in `run_design.R` runs the proposed design together with BF-BOIN, TITE-BOIN-ET, TITE-BOIN12, and BF-BOIN-ET for one scenario. In its output:

- `output$selection`: MTD/OBD selection percentages (`Dose 1`–`Dose 5`) and `early.stop`. The paper’s proposed method is `MTD.ours2` / `OBD.ours2`.
- `output$EN`: average patients by dose, total `EN`, overdose `EN.Overdose`, and `DLT_pts`. Use the `*.total` rows (proposed method: `ours2.total`).
- `output$duration`: mean duration (months) for each method.

The script’s bottom example calls `MAIN.func()` once (`nsimu = 10` for a quick check; the paper used 5000).
