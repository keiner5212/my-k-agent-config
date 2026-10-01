---
name: python-data-analysis
description: >-
  Load for Python analysis of tabular or array data, including pandas, NumPy,
  data cleaning, aggregation, statistics, reproducible reports, and plots.
---

# Python Data Analysis

## When to use

- Inspect, clean, join, aggregate, or visualize CSV, Excel, JSON, or database data.
- Compute descriptive statistics, correlations, or defined statistical tests.
- Build a repeatable analysis notebook or script using pandas and NumPy.

## Hard rules

- Preserve raw input; write cleaned and derived data to separate outputs.
- Inspect schema, row count, missingness, duplicates, and ranges before transformation.
- Define missing-data, outlier, and duplicate policies before applying them.
- Use vectorized pandas or NumPy operations; avoid row loops unless unavoidable.
- Record source, extraction time, filters, package versions, and random seed.
- Do not infer causality from correlation or use tests without stated assumptions.
- Keep units, time zones, and currency definitions explicit.

## Workflow

1. Load data with explicit dtypes, date parsing, encoding, and source metadata.
2. Profile shape, schema, nulls, duplicates, distributions, and data-quality failures.
3. Clean with documented rules; validate row-count changes and key uniqueness.
4. Transform through named, reproducible steps; avoid mutating raw data.
5. Analyze against a stated question and report uncertainty where appropriate.
6. Export results, figures, and enough metadata to reproduce the run.

## Checklist

- Input path, date range, filters, and row counts recorded.
- Join cardinality checked before and after merge.
- Categories, missing values, and outliers handled intentionally.
- Random seed set for stochastic analysis.
- Plot has title, labeled axes with units, readable scale, legend when needed, and source note.
- Colors and markers remain distinguishable in grayscale.

## Anti-patterns

- Calling `dropna()` or `drop_duplicates()` without defining affected columns.
- Using `iterrows()` for vectorizable transformations.
- Merging without validating key uniqueness.
- Truncated axes or color scales that misrepresent differences.
- Presenting a chart without units, timeframe, sample size, or aggregation method.

## Refs

- pandas: tables, joins, grouping, and time series.
- NumPy: vectorized arrays and numerical operations.
- Matplotlib: publication-ready figures; Seaborn: statistical plot defaults.
- SciPy stats: tests and distributions with documented assumptions.
