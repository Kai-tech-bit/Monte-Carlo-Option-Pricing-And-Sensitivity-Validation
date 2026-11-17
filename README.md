# Monte Carlo Option Pricing and Sensitivity Validation

A notebook-based quantitative-finance study of option pricing, sensitivities, and numerical validation.

## Repository status

Initial structure only. The final notebook and generated results still need to be uploaded and reviewed. No numerical findings or validation outcomes are asserted here.

## Structure

```text
code/
  README.md
  pricing_and_validation.ipynb  # upload your final saved notebook here
results/
  README.md
  <run-folder>/               # upload an extracted results bundle here
.gitignore
```

The notebook and run folder shown above are intended upload destinations, not files already included in this initial commit.

## Planned study scope

- European option pricing and Black-Scholes-Merton comparisons.
- Monte Carlo sampling, uncertainty, and variance reduction.
- Greek estimation and diagnostics.
- Asian options.
- American/Bermudan exercise experiments.
- Discretely monitored, zero-rebate single-barrier options.

The final scope must be checked against the uploaded notebook.

## Workflow

1. Upload the final saved notebook to `code/pricing_and_validation.ipynb`.
2. Upload the extracted output bundle as one run folder inside `results/`. Preserve its internal layout and filenames.
3. Review execution errors, validation tables, benchmarks, and consistency between the notebook and outputs.
4. Document verified dependencies, run instructions, findings, and limitations.

## Reproducibility and limitations

Exact dependencies and execution instructions are pending inspection of the final notebook. A successful notebook run or archive integrity check does not establish pricing correctness. Saved artifacts must be associated with their generating configuration and reviewed for stale outputs.

Do not upload credentials, private datasets, or other sensitive information. No license is selected in this initial structure.
