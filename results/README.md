# Option-pricing results bundle

## Contents
- artifacts/: saved outputs copied from the notebook's export directories.
- configuration_snapshot.json: available settings captured at packaging time.
- environment.json: available environment information and source directories.
- inventory.csv: packaged file sizes and SHA-256 checksums.
- SHA256SUMS.txt: checksums for all other packaged files.
- notebook/: a saved notebook, only if explicitly supplied.

## Important limitations
Packaging does not rerun analysis or certify financial or numerical accuracy.
Files already present in the source folders may come from earlier runs.
A configuration snapshot does not prove every artifact used those settings.
Previously exported validation tables are preserved, not reinterpreted.
Unsaved notebook state, live figures, and unexported tables are not captured.
The notebook file alone does not guarantee full reproducibility.

Review validation tables, benchmark comparisons, filenames and timestamps
before treating this bundle as a completed study or publishing it.
Review all contents for sensitive information before sharing.
