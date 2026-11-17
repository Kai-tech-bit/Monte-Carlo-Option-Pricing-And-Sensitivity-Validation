# Monte Carlo Option Pricing and Sensitivity Validation

A reproducible research study of Monte Carlo pricing, variance reduction,
European sensitivities, path-dependent payoffs, and early-exercise policies
under a common risk-neutral geometric Brownian motion model.

The project connects analytical benchmarks with numerical experiments:
European options provide a reference for pricing and Greek accuracy,
Asian options introduce averaging and geometric controls,
American/Bermudan experiments introduce exercise-policy estimation,
and barrier options introduce monitored path events.

The repository contains the research notebook and a saved quick-mode results
bundle with 24 figures, 27 CSV tables, configuration records, and checksums.

## Research questions

1. How closely do Monte Carlo prices reproduce analytical European values?
2. How do antithetic sampling and control variates change sampling uncertainty
   and repeated-run error?
3. How reliably do common-random-number finite differences reproduce
   analytical Greeks?
4. How effective is a geometric-average control for arithmetic Asian options?
5. How do estimated early-exercise policies compare with European holding
   values and tree benchmarks?
6. How do barrier location and observation frequency affect knock-in and
   knock-out values?

## Start here

- [Research notebook](code/pricing_and_validation.ipynb)
- [Saved results bundle](results/OPTION_PRICING_AND_SENSITVITY/)
- [Figures](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_01_figures/)
- [Numerical tables](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/)
- [Run configuration](results/OPTION_PRICING_AND_SENSITVITY/configuration_snapshot.json)
- [Recorded environment](results/OPTION_PRICING_AND_SENSITVITY/environment.json)

The results directory name above matches the repository's current spelling.

## Model and valuation conventions

Under the risk-neutral measure,

$$
dS_t=(r-q)S_t\,dt+\sigma S_t\,dW_t.
$$

At simulated observation dates, the stock follows exact GBM transitions:

$$
S_{t+\Delta t}
=
S_t\exp\left[
(r-q-\tfrac12\sigma^2)\Delta t
+\sigma\sqrt{\Delta t}\,Z
\right],
\qquad Z\sim N(0,1).
$$

Contract values are discounted expected payoffs under this model.

### Baseline inputs

| Parameter | Value |
|---|---:|
| Initial spot | 100 |
| Strike | 100 |
| Risk-free rate | 5% |
| Continuous dividend yield | 0% |
| Annual volatility | 20% |
| Maturity | 1 year |

Rates are continuously compounded. Prices are per unit of underlying,
without a contract multiplier. Individual experiments may change baseline
inputs; the notebook and configuration records define those changes.

These are synthetic model experiments, not market-calibrated valuations
or historical trading-performance tests.

## Implemented study components

| Component | Methods and experiments |
|---|---|
| European calls and puts | BSM reference values, plain Monte Carlo, antithetic sampling, discounted-stock control variates |
| Convergence and uncertainty | Independent replications, signed error, RMSE, reported standard errors, interval coverage, timing diagnostics |
| European Greeks | Analytical delta, gamma, vega, theta and rho; common-random-number central differences; bump and spot-grid diagnostics |
| Arithmetic Asian calls and puts | Discrete averaging, plain and antithetic simulation, geometric-average control, monitoring-contract comparisons |
| Early-exercise experiments | LSMC policy estimation, separate training/evaluation samples, European/Bermudan/American tree comparisons, basis and exercise-grid sensitivity |
| Single-barrier calls and puts | Up/down knock-in and knock-out payoffs, discrete monitoring, first-observed-hit records, barrier-level and monitoring sensitivity |

## Estimator design

### Plain Monte Carlo

The estimator averages discounted simulated payoffs. Its reported standard
error is based on the independent observations used in that experiment.

### Antithetic sampling

Each normal draw is paired with its negative. The two discounted payoffs
are averaged before estimating uncertainty.

One pair average is one independent observation. Individual antithetic
legs are not treated as independent samples.

### European control variate

The discounted terminal stock is used as a control:

$$
X=e^{-rT}S_T,
\qquad
E[X]=S_0e^{-qT}.
$$

The adjusted payoff is

$$
Y^*=Y-\hat\beta(X-E[X]).
$$

The coefficient is fitted on a separate pilot sample. Pilot evaluations
are recorded in the exported results.

The main comparison holds production payoff evaluations fixed, not total
computational cost. Control-variate production estimates therefore use
additional pilot work.

### Asian geometric control

Arithmetic and geometric averages are simulated on the same observation
schedule. The matching geometric-average option provides an analytical
control expectation.

For the Asian contracts, the initial spot is excluded from the average.
Changing the number of monitoring dates changes the contract itself.

### Greek estimation

Finite differences use common random numbers across bumped valuations.
Per-draw finite-difference contributions are formed before estimating
means and standard errors, preserving dependence between bumped prices.

Gamma receives particular attention through bump-size and spot-grid
diagnostics.

### Early-exercise policy estimation

The saved configuration separates policy training from evaluation.

The evaluation estimates the value of a learned exercise policy.
It should not be confused with proof that the optimal stopping policy
has been recovered.

## Saved run configuration

The committed results bundle records quick mode with seed `20261117`.

| Experiment | Configuration |
|---|---|
| European pricing | 20,000 production payoff evaluations |
| European control pilot | 4,000 observations |
| Convergence | 24 replications at 1,000, 4,000 and 16,000 production evaluations |
| Base Greek experiment | 40,000 draws |
| Asian pricing | 12,000 production paths and 3,000 pilot paths for the control |
| Asian monitoring | 52 intervals |
| Early-exercise training | 15,000 paths |
| Early-exercise evaluation | 20,000 paths |
| Exercise grid | 50 intervals |
| Regression degree | 2 |
| Barrier pricing | 20,000 paths and 96 monitoring intervals |
| Nested barrier schedules | 12, 24, 48 and 96 intervals |

The full configuration is available in
[configuration_snapshot.json](results/OPTION_PRICING_AND_SENSITVITY/configuration_snapshot.json).

## Results

The following values summarize the committed run. They are not promises
about every seed, parameter combination, or hardware environment.

### European pricing

| Contract | Method | Estimate | Standard error | BSM |
|---|---|---:|---:|---:|
| Call | Plain | 10.416565 | 0.103379 | 10.450584 |
| Call | Antithetic | 10.493298 | 0.073885 | 10.450584 |
| Call | Control | 10.443057 | 0.039526 | 10.450584 |
| Put | Plain | 5.444663 | 0.060600 | 5.573526 |
| Put | Antithetic | 5.574886 | 0.046746 | 5.573526 |
| Put | Control | 5.577333 | 0.039406 | 5.573526 |

The control estimator has the smallest reported standard error for both
baseline contracts in this comparison.

The plain put's saved interval does not contain BSM. An individual
interval miss is reported as a statistical outcome, not automatically
classified as an implementation failure.

[European comparison CSV](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/european_comparison.csv)

### Repeated-run convergence

At 16,000 production payoff evaluations:

| Method | RMSE versus BSM | Observed interval coverage |
|---|---:|---:|
| Plain | 0.113556 | 95.83% |
| Antithetic | 0.075676 | 95.83% |
| Control | 0.041968 | 95.83% |

These figures use 24 replications per method. The control additionally
uses a 4,000-observation pilot in each replication.

The control has the lowest RMSE at each tested budget in this run.
Coverage estimates are noisy with only 24 replications and do not
establish universal 95% coverage.

[Convergence table](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/convergence_summary.csv)
·
[Convergence and coverage figure](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_01_figures/convergence_and_coverage.png)

### European sensitivities

The spot-grid summary covers 13 contracts for each combination of
call/put and the five reported Greeks: 130 comparisons in total.

Analytical benchmarks fall within 128 of the 130 reported pointwise
intervals. Put gamma accounts for the two interval misses.

This is a descriptive result for correlated spot-grid diagnostics,
not an independent coverage experiment or a guarantee of negligible
finite-difference bias.

Greek units:

- Delta: per unit change in spot.
- Gamma: per squared unit of spot.
- Vega: per 1.00 absolute change in volatility.
- Rho: per 1.00 absolute change in the rate.
- Theta: per calendar year elapsed.

Vega and rho per percentage point are the reported raw sensitivities
divided by 100.

[Greek spot-grid summary](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/greeks_spot_grid_summary.csv)
·
[Bump sensitivity table](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/greek_bump_sensitivity.csv)

### Arithmetic Asian options

| Contract | Plain estimate | Plain SE | Geometric-control estimate | Control SE |
|---|---:|---:|---:|---:|
| Call | 5.913302 | 0.073937 | 5.853514 | 0.002014 |
| Put | 3.408759 | 0.048860 | 3.389283 | 0.001223 |

The geometric control substantially reduces reported sampling uncertainty
in these baseline experiments.

The geometric analytical price benchmarks the control contract.
It is not an analytical benchmark for the arithmetic Asian payoff.

[Asian comparison CSV](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/asian_comparison.csv)
·
[Asian intervals figure](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_01_figures/asian_intervals.png)

### American/Bermudan exercise experiments

| Case | Evaluated LSMC policy | SE | Bermudan tree | American tree |
|---|---:|---:|---:|---:|
| Put, no dividend | 6.069073 | 0.051159 | 6.075485 | 6.086383 |
| Call, no dividend | 10.485890 | 0.100736 | 10.440591 | 10.440591 |
| Call, with dividend | 6.570678 | 0.063671 | 6.529048 | 6.537470 |

The put and dividend-paying call estimates are close to the reported
tree values at the displayed sampling precision.

The non-dividend-call policy illustrates an important policy-quality
limitation: 17.765% of evaluation paths are paid early, and its paired
premium relative to holding is -0.055162 with SE 0.022969.

A price estimate near the European benchmark therefore does not imply
that the learned exercise decisions are optimal. This case is retained
as a diagnostic finding, not presented as successful recovery of the
optimal exercise policy.

[Policy comparison](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/american_policy_comparison.csv)
·
[Policy benchmark figure](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_01_figures/american_policy_benchmarks.png)

### Discretely monitored barrier options

Barrier contracts use:

- A single upper or lower barrier.
- Knock-in or knock-out activation.
- Equally spaced discrete monitoring, including time zero and maturity.
- Touching or crossing as a hit.
- Zero rebate.
- Any activated or surviving vanilla payoff paid at maturity.

Baseline barrier results:

| Contract family | Barrier | Knock-in | Knock-out | Shared-path vanilla MC |
|---|---:|---:|---:|---:|
| Down call | 85 | 0.385221 | 10.035689 | 10.420911 |
| Up call | 115 | 9.786701 | 0.634210 | 10.420911 |
| Down put | 85 | 4.860740 | 0.797982 | 5.658722 |
| Up put | 115 | 0.450318 | 5.208405 | 5.658722 |

Matching knock-in and knock-out payoffs partition the vanilla payoff
on the same paths. The implementation checks this identity directly.

Observation frequency experiments use nested schedules on common paths.
They compare different monitored contracts; they are not independently
benchmarked continuous-barrier prices.

[Barrier price table](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/barrier_price_comparison.csv)
·
[Barrier monitoring figure](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_01_figures/barrier_monitoring_sensitivity.png)

## Validation evidence

All rows in the exported general and barrier validation tables are marked
as passed.

The general table includes:

- Put-call parity and analytical price bounds.
- A known European call reference value.
- Expiry and zero-volatility cases.
- Positive simulated terminal stock values.
- Analytical Greeks checked against deterministic finite differences.
- Seed reproducibility.
- Asian arithmetic/geometric payoff ordering.
- The one-observation Asian/European identity.

Barrier checks include:

- Exact-transition terminal consistency.
- Pathwise in-out parity.
- Payoff bounds.
- Initially touched barriers.
- Barrier-level ordering.
- Nested-monitoring monotonicity.

These checks establish specific implementation properties. They do not
constitute independent model certification, a complete audit, or proof
that every learned exercise policy is optimal.

- [General checks](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/validation_checks.csv)
- [Barrier checks](results/OPTION_PRICING_AND_SENSITVITY/artifacts/source_02_tables/barrier_validation_checks.csv)

## Running the study

### Dependencies

The saved environment records:

| Dependency | Recorded version |
|---|---|
| Python | 3.13.18 |
| NumPy | 2.4.6 |
| pandas | 2.2.3 |
| SciPy | 1.16.3 |
| Matplotlib | 3.10.0 |

These are recorded run versions, not a tested compatibility range.

A notebook frontend such as Jupyter or Google Colab is also needed.

### Execution

1. Clone or download the repository.
2. Open `code/pricing_and_validation.ipynb`.
3. In the setup cell, explicitly set `PROJECT_ROOT` to the repository root.
4. Select `MODE = 'quick'` or `MODE = 'full'`.
5. Restart the kernel and run the cells in order.
6. Run the final export and packaging cells after all analysis sections.

The current setup derives paths from `Path.cwd()` and automatically
adjusts only for a folder named `notebooks`, not `code`. Setting
`PROJECT_ROOT` explicitly avoids accidentally creating `code/results/`
when launching from the code directory.

Raw exports are written under `PROJECT_ROOT/results/figures/` and
`PROJECT_ROOT/results/tables/`. The committed bundle is a separate saved
snapshot of those exports.

Use clean export directories for a new run so the packaging step does
not collect stale files.

The core pricing experiments use synthetic inputs and do not require
market-data credentials or an external price CSV.

## Repository layout

```text
.
├── README.md
├── .gitignore
├── code/
│   └── pricing_and_validation.ipynb
└── results/
    └── OPTION_PRICING_AND_SENSITVITY/
        ├── artifacts/
        │   ├── source_01_figures/
        │   └── source_02_tables/
        ├── configuration_snapshot.json
        ├── environment.json
        ├── inventory.csv
        ├── README.md
        └── SHA256SUMS.txt
```

The layout above shows the principal research deliverables. Any retained
setup documentation is ancillary.

## Scope and interpretation

This project is useful for studying numerical pricing methods, estimator
design, uncertainty reporting, and the relationship between contract
features and computational methods.

It is not a production trading platform or an enterprise risk engine.

Important boundaries:

- Constant-parameter GBM is the common model.
- Inputs are not calibrated to a market volatility surface.
- Sampling intervals do not measure model risk.
- Greek intervals do not include finite-difference bias.
- Early-exercise estimates depend on the learned policy and exercise grid.
- Barrier crossings between observation dates are not detected.
- The barrier module does not implement continuous monitoring, rebates,
  double barriers, or barrier Greeks.
- Timings are environment-dependent.
- Quick-mode results are exploratory numerical evidence, not exhaustive
  testing across all parameter regimes.

## Artifact integrity

The saved bundle includes an inventory and SHA-256 checksums.

These support checking file integrity after transfer. They do not prove
financial correctness, establish that no stale files were collected, or
by themselves verify correspondence between every artifact and the
current notebook source.

## Overall conclusion

In the committed quick-mode experiments, control variates reduce European
pricing error and reported sampling uncertainty, while the geometric
control is particularly effective for the arithmetic Asian examples.

Common-random-number finite differences reproduce the analytical Greek
profiles across most reported pointwise intervals, with gamma requiring
careful interpretation.

Early-exercise experiments show why policy diagnostics matter alongside
price comparisons. Barrier experiments demonstrate how monitored path
events can be implemented and checked using payoff identities and
nested observation schedules.

The central contribution is the integrated comparison of pricing methods,
uncertainty, sensitivities, exercise decisions, and path-dependent
contract specifications within one documented research workflow.
