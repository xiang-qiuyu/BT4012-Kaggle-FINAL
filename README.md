# BT4012 Fraudulent Bitcoin Transaction Detection

This repository contains the reproducible modelling pipeline used for the
BT4012 Kaggle fraudulent Bitcoin transaction detection assignment.

## Environment

The final notebook was executed using Python 3.13.9.

Key package versions:

- pandas 3.0.6
- NumPy 2.5.4
- scikit-learn 1.7.2
- SciPy 1.16.3
- LightGBM 4.7.0
- Matplotlib 3.11.2

Install the required Python packages with:

```bash
pip install -r requirements.txt
```

## Data

Place the four competition files in a folder named `data/` at the repository
root:

```text
data/
├── train.csv
├── test.csv
├── txs_edgelist.csv
└── sample_submission.csv
```

The competition data are not included in this repository.

## Running the Notebook

Run `E1157128.ipynb` from the repository root from top to bottom.

The notebook covers:

- exploratory data analysis;
- chronological temporal validation;
- graph-based feature engineering;
- recency-weighted training;
- class-imbalance treatment;
- model comparison and selection;
- final LightGBM training;
- Random Forest rank ensembling; and
- submission generation.

All stochastic models use a fixed random seed (`random_state=42`).

The notebook automatically creates the `submissions/` directory and generates:

```text
submissions/v14_class_weight4_recency_graph.csv
submissions/v261_v14_rf_rank_90_10.csv
```

## Final Models

### V14 — Primary Model

V14 combines richer one-hop graph features, exponential recency weighting,
4× positive-class weighting and LightGBM.

- 7-step temporal validation ROC-AUC: 0.982081
- 14-step temporal validation ROC-AUC: 0.937623
- Public leaderboard ROC-AUC: 0.95422

### V26.1 — Diversified Second Submission

V26.1 combines percentile ranks from V14 and a Random Forest:

- 90% V14 rank
- 10% Random Forest rank

Its performance was:

- 7-step temporal validation ROC-AUC: 0.980775
- 14-step temporal validation ROC-AUC: 0.933971
- Public leaderboard ROC-AUC: 0.95450