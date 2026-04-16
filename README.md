# Project Bombini: AI-Driven Dynamic Pricing System

An end-to-end machine learning pipeline that recommends revenue-maximising prices for grocery retail, built as part of the Digital Driven Business master's program at Hogeschool van Amsterdam. Combines demand forecasting, pricing optimisation, and automated PDF reporting.

## 📄 Full Report

The complete analysis, methodology, results, and visualisations are documented in the auto-generated PDF report:

**➡️ [View Final_Generated_PDF](Final_Generated_PDF.pdf)**

This report is produced automatically by the pipeline and contains every chart, model diagnostic, and finding from the project.

---

## Authors

Ariana Ferrara & Irem Yikilmaz

## What This Project Does

This is a complete dynamic pricing pipeline for the GROCERY department of a US retail chain. It takes raw transaction data as input and produces:

1. A trained demand model that predicts weekly unit sales for any product at any price
2. A pricing agent that recommends revenue-maximising prices within a fairness constraint
3. A backtesting evaluation comparing the agent's recommendations against actual store results
4. An automated PDF report documenting every step with charts and explanations

## Data

This project uses proprietary retail transaction data provided by Hogeschool van Amsterdam as part of the Digital Driven Business program. The data is not publicly available and is not included in this repository. The analysis covers the grocery department of a US retail chain.

Because the data cannot be shared, the code in this repository is not runnable end-to-end by external viewers. The Final_Generated_PDF linked above contains the complete outputs and findings.

## Project Structure

<details>
<summary>📁 Click to expand full structure</summary>

```
system development/                     <-- working directory
├── products.parquet                    <-- raw data (not in repo)
├── transactions.parquet                <-- raw data (not in repo)
├── 1__eda_feature_engineering_finale.ipynb
├── 2__modelling_finale_parallel_included.ipynb
├── 3__pricing_agent_backtesting.ipynb
├── requirements.txt
│
└── bombini/                            <-- pipeline code (this folder)
    ├── main.py                         # orchestrator: runs everything
    ├── config.py                       # all constants, paths, thresholds
    ├── eda/
    │   ├── data_loader.py              # DataLoader: load, merge, department selection
    │   ├── cleaning.py                 # DataCleaner: outliers, aggregation, filters
    │   ├── charts.py                   # EDACharts: all 17 EDA visualisations
    │   └── feature_engineering.py      # FeatureEngineer: 26 features, split, scale
    ├── modelling/
    │   └── models.py                   # SequentialModels, ParallelModels, Diagnostics
    ├── pricing/
    │   └── agent.py                    # PricingSimulator, Backtester
    ├── utils/
    │   ├── logging_setup.py            # logging config + save_fig helper
    │   └── report_builder.py           # PDF report generator (reportlab)
    ├── outputs/                        # generated automatically
    │   ├── bombini_report.pdf          # full PDF report
    │   └── *.png                       # individual chart files
    ├── requirements.txt
    └── README.md
```

</details>

## How to Run

### Prerequisites

Python 3.10+ and the required packages:

```bash
pip install -r requirements.txt
pip install reportlab
```

The data files (`products.parquet` and `transactions.parquet`) must be in the parent directory (the same folder as the Jupyter notebooks). These files are not included in this repository.

### Running the Full Pipeline

From a terminal, navigate to the bombini folder and run:

```bash
cd path/to/bombini
python main.py
```

This runs all four phases in sequence and takes approximately 20-30 minutes. When finished, the PDF report is generated at `outputs/bombini_report.pdf`.

### Running Individual Phases

To run only one phase (useful for testing or re-running after changes):

```bash
python main.py --phase eda      # Phase 1: EDA + feature engineering (~5 min)
python main.py --phase model    # Phase 2: demand modelling (~15 min)
python main.py --phase price    # Phase 3+4: pricing agent + backtesting (~10 min)
```

Phase 1 must run first because it creates `modeling_inputs.pkl` which Phase 2 needs. Phase 2 must run before Phase 3 because it creates `best_model.pkl`.

### What Gets Generated

After a full run, you will find:

| File | Description |
|------|-------------|
| `outputs/bombini_report.pdf` | Full PDF report with all charts and explanations |
| `outputs/*.png` | Individual chart files (44 total) |
| `modeling_inputs.pkl` | Processed features, train/test data (in parent dir) |
| `best_model.pkl` | Trained Random Forest model (in parent dir) |
| `feature_cols.pkl` | Feature list for the slim model (in parent dir) |
| `scaler.pkl` | StandardScaler fitted on training data (in parent dir) |
| `label_encoder.pkl` | Category encoder (in parent dir) |
| `pricing_recommendations.csv` | 52-week pricing schedule per product (in parent dir) |
| `pricing_summary.csv` | Annual summary per product (in parent dir) |

## Relationship to Jupyter Notebooks

The notebooks and the pipeline contain the same logic. The notebooks have inline outputs and detailed markdown explanations for interactive exploration. The pipeline is the production version: modular code, structured logging, and an auto-generated PDF report. Both produce identical numerical results.

## Design Principles

- **Configuration in one place**: all thresholds, paths, and constants live in `config.py`
- **Logging instead of print**: every module uses Python's logging module for structured output
- **Charts saved to disk**: every visualisation is saved as PNG and embedded in the PDF report
- **Modular structure**: to add a new model, edit `modelling/models.py`; to add a new feature, edit `eda/feature_engineering.py`
