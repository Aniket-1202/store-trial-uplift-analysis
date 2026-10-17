# Store Trial Uplift Analysis

A reproducible analysis of a fictional retail pilot: compare a set of pilot stores with matched non-pilot stores, then estimate the change associated with the trial using difference-in-differences (DiD). The analysis also bootstraps matched-store pairs to show uncertainty around the estimated lift.

**All data in this repository are synthetic.** The intervention effect is simulated; the output is not evidence about a real retailer or a real experiment.

Open [`analysis.html`](analysis.html) directly in a browser for the standalone interactive report. The report is generated from the included synthetic data and checked output files; rerun the analysis and rebuild it if you change the data.

## Quick start

Requires Python 3.10+; no third-party packages are needed.

```bash
python generate_data.py
python scripts/analyze_trial.py
python -m unittest discover -s tests -v
```

The analysis writes `reports/summary.json`, `reports/trial-report.md`, `reports/matched-stores.csv`, and `reports/weekly-comparison.csv`. Output files are ignored by Git; regenerate them locally with the command above.

## Analysis workflow

1. Validate the store-week panel and configured pre/trial periods.
2. Calculate pre-trial average sales and customers for pilot and non-pilot stores.
3. Greedily match each pilot store to a unique control using standardized distance on those **pre-trial** characteristics only.
4. Compute each matched pair's difference-in-differences: `(pilot trial − pilot pre) − (control trial − control pre)`.
5. Report average absolute and relative lift, and a seeded percentile bootstrap confidence interval over matched pairs.

Matching uses no trial-period outcomes. The bootstrap resamples matched store pairs—not individual weeks or transactions—so the unit of analysis remains the store pair.

## Repository structure

```text
data/store_weekly_sales.csv  # fictional 60-store, 52-week panel
config/trial.json            # pilot IDs, time windows, and seed
src/uplift_analysis.py       # validation, matching, DiD, bootstrap
scripts/analyze_trial.py     # command-line runner and reports
reports/                     # generated outputs; git-ignored
tests/                       # deterministic unit tests
```

## Important limitations

This is a compact learning example, not a full causal-inference toolkit. Greedy matching can depend on assignment order and does not ensure covariate balance; inspect the match report. Eight pilot stores make the bootstrap interval coarse. The interval describes variation among these synthetic matched pairs under the resampling scheme; it does not account for every design or time-series uncertainty. DiD depends on assumptions such as comparable pre-trends and no spillovers, which this demo does not establish. No result should be described as a verified business impact.

For a real study, consult a qualified analyst, document assignment and concurrent changes, inspect pre-trends and balance, pre-register outcomes, and use an appropriately powered design.

## License

MIT.
