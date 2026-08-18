# Puffer ABR Capstone

**Real-World Performance of Adaptive Bitrate Streaming Algorithms: A Network Telemetry Analysis Using the Puffer Dataset**

QM640 Data Analytics Capstone — Thalaivanan N., Walsh College

## Overview

This repository supports the Synopsis, Interim Report, and Final Report for a data analytics capstone applying classical inferential statistics (one-way ANOVA, multiple linear regression, logistic regression) to Stanford University's [Puffer dataset](https://puffer.stanford.edu/), a live, randomized-controlled-trial video streaming platform.

Four research questions are addressed:
- **RQ1**: Does rebuffering ratio differ significantly across ABR/congestion-control algorithm pairings? (one-way ANOVA)
- **RQ2**: How well do network round-trip time and delivery rate predict rebuffering? (multiple regression)
- **RQ3**: Does congestion window size differ significantly across algorithm pairings? (one-way ANOVA)
- **RQ4**: Does delivered video quality (SSIM) predict the likelihood of a rebuffering event? (logistic regression)

## Study Window

**January 1–31, 2026** (31 days), N = 63,044 sessions after cleaning, plus a bounded 620,000-row chunk-level sample for RQ4.

## Repository Structure

```
puffer-abr-capstone/
├── README.md
├── .gitignore
├── notebooks/
│   └── puffer_pipeline.ipynb       # Full pipeline: ingest, clean, engineer features, EDA, model (Colab-ready)
├── data/
│   └── processed/
│       ├── session_stats.parquet          # Session-level table (N=63,044), used for RQ1-RQ3
│       ├── chunk_level_sample.parquet      # Bounded chunk-level sample (620,000 rows), used for RQ4
│       ├── table_correlation_matrix.csv    # EDA output: network telemetry correlation matrix
│       └── table_descriptive_stats.csv     # EDA output: descriptive statistics
├── figures/
│   └── figure1_rebuf_by_algorithm.png      # Rebuffering ratio by algorithm pairing (boxplot)
└── docs/
    ├── QM640_Synopsis.docx
    ├── QM640_Interim_Report.docx
    └── QM640_Final_Report.docx
```

## Reproducing This Analysis

1. Open `notebooks/puffer_pipeline.ipynb` in Google Colab (or a local Jupyter environment).
2. Set `START_DATE`, `END_DATE`, and `SAMPLE_DAYS` in the configuration cell (validate on a few days before running the full window — each raw Puffer day is several GB).
3. Run all cells. The notebook downloads directly from Puffer's public data release, cleans the data, engineers features, runs EDA, and fits all four models.

## Data Availability

**Raw Puffer CSVs are intentionally not stored in this repository.** Each raw day is several gigabytes — far beyond what's practical to store in Git — and Puffer's own team asks users not to duplicate their data unnecessarily. Raw data can be regenerated at any time by running `notebooks/puffer_pipeline.ipynb`, which downloads directly from Puffer's public bucket (cited in the reports' Data Description section).

The **processed, analysis-ready data** (`data/processed/`) is included directly, so results can be inspected or reanalyzed without re-running the full pipeline.

## Data Cleaning Summary (31-day window)

| Step | Rows/Sessions Affected | Rate |
|---|---|---|
| Missing network telemetry | 0 rows | 0.000% |
| RTT outliers (99.9th percentile) | 173,470 rows | 0.100% |
| Minimum RTT outliers (99.9th percentile) | 171,581 rows | 0.099% |
| Duplicate chunk-send records | 3,093,767 rows | 1.787% |
| Short sessions (<30s) | 29,147 of 92,198 sessions | 31.613% |
| Impossible rebuffering ratio (>100%) | 7 of 63,051 sessions | 0.011% |

Final sample: **N = 63,044 sessions**.

## Key Findings (see Final Report for full detail)

- RQ1: Rebuffering ratio differs significantly across algorithm pairings (F = 10.902, p < .001); pairings 2244 and 2245 drive the effect.
- RQ2: Network telemetry significantly predicts rebuffering (R² = 0.078, F = 1790, p < .001), though explanatory power is modest.
- RQ3: Congestion window size differs significantly across pairings (F = 106.044, p < .001).
- RQ4: SSIM significantly predicts rebuffer-event odds (p < .001); a quasi-complete separation caveat is discussed in the Final Report's Limitations section.

## Technology Stack

Python 3 (Google Colab) · pandas, numpy · scipy, statsmodels · matplotlib · requests · pyarrow (Parquet)

## Author

Thalaivanan N. — Walsh College, QM640 Data Analytics Capstone, Summer 2026 (Term-3)
Mentor: Mr. Smitraj Raut
