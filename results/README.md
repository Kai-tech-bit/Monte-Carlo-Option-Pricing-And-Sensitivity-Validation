# Saved research results

Upload the extracted results bundle here as one run folder, for example:

```text
results/
  README.md
  run_YYYYMMDD_HHMMSS/
    artifacts/
    configuration_snapshot.json
    environment.json
    inventory.csv
    SHA256SUMS.txt
    README.md
```

The example run folder is not present yet. You may keep the generated bundle's original top-level folder name instead. Do not add an unnecessary second `results/` wrapper.

## Upload procedure

1. Extract the ZIP on your PC.
2. Open this `results/` directory on GitHub.
3. Choose Add file > Upload files.
4. Drag the extracted top-level run folder into the upload area.
5. Verify the proposed paths before committing.

Keep each run's internal folder layout and file contents unchanged if you want its existing relative-path checksums to remain applicable. Keep the ZIP locally as a backup; uploading both the archive and all extracted contents is not necessary for this review.

## Review priorities

- Validation tables, including any failed checks.
- Benchmark comparisons and uncertainty estimates.
- Pricing, Greek, exercise, and barrier figures.
- Configuration and environment records.
- Inventory and artifact checksums.

A checksum establishes file integrity, not numerical correctness. Do not mix old exports with a new run or publish sensitive information.
