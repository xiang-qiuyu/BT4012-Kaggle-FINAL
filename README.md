# BT4012 Fraudulent Bitcoin Transaction Detection

This repository contains the reproducible modelling pipeline for the BT4012 Kaggle Fraudulent Bitcoin Transaction Detection assignment.

The final notebook covers the end-to-end workflow, including data loading, exploratory analysis, feature engineering, chronological validation, model development, final model training, and submission generation.

## Repository Structure

```text
BT4012-Kaggle-FINAL/
├── E1157128.ipynb
├── README.md
├── requirements.txt
├── .gitignore
├── data/                  # supplied competition data; not included in GitHub
└── submissions/           # generated automatically; not included in GitHub
```

### Main Files

- `E1157128.ipynb` — complete modelling and evaluation notebook
- `requirements.txt` — exact Python package versions used for the final notebook
- `.gitignore` — excludes local environments, supplied competition data, generated submissions, and temporary files

## Data Setup

The competition data are not included in this repository.

After cloning the repository, create a folder named `data` in the repository root and place the four supplied competition files inside it:

```text
data/
├── train.csv
├── test.csv
├── txs_edgelist.csv
└── sample_submission.csv
```

The notebook reads the files using relative paths:

```text
data/train.csv
data/test.csv
data/txs_edgelist.csv
data/sample_submission.csv
```

No machine-specific file paths are required.

## Environment Setup

The final notebook was executed using:

```text
Python 3.13.9
```

All required Python package versions are pinned in `requirements.txt`.

### Windows

From the repository root, create a fresh virtual environment using a Python 3.13 installation:

```powershell
py -3.13 -m venv .venv
```

Activate it:

```powershell
.venv\Scripts\activate
```

Install the required packages:

```powershell
python -m pip install -r requirements.txt
```

### macOS / Linux

Create a fresh virtual environment:

```bash
python3 -m venv .venv
```

Activate it:

```bash
source .venv/bin/activate
```

Install the required packages:

```bash
pip install -r requirements.txt
```

## Reproducing the Results

To reproduce the modelling pipeline from a fresh environment:

1. Clone this repository.
2. Create the `data/` directory and add the four supplied competition files using the filenames shown above.
3. Create and activate a fresh Python environment.
4. Install the dependencies:

   ```bash
   pip install -r requirements.txt
   ```

5. Open `E1157128.ipynb`.
6. Select the newly created virtual environment as the Jupyter kernel.
7. Restart the kernel.
8. Run all notebook cells sequentially from top to bottom.

No manually precomputed intermediate files are required.

The notebook creates the `submissions/` directory automatically when the final prediction files are generated.

## Final Models

Two final candidate submissions are generated.

### V14 — Primary Model

V14 combines:

- original transaction attributes
- engineered one-hop graph features
- neighbour transaction-attribute aggregates
- exponential recency weighting
- 4× positive-class weighting
- LightGBM

The final LightGBM specification uses:

```text
n_estimators = 500
learning_rate = 0.05
num_leaves = 31
random_state = 42
```

The notebook reports approximately:

```text
7-step temporal validation ROC-AUC:  0.98208
14-step temporal validation ROC-AUC: 0.93762
Public leaderboard ROC-AUC:          0.95422
```

### V26.1 — Diversified Ensemble

V26.1 combines percentile-ranked predictions from:

```text
90% V14 LightGBM
10% Random Forest
```

The Random Forest specification uses:

```text
n_estimators = 500
max_features = 0.5
min_samples_leaf = 5
random_state = 42
```

The notebook reports approximately:

```text
7-step temporal validation ROC-AUC:  0.98078
14-step temporal validation ROC-AUC: 0.93397
Public leaderboard ROC-AUC:          0.95450
```

## Generated Submission Files

Running the notebook from top to bottom generates the final submission files:

```text
submissions/v14_class_weight4_recency_graph.csv
submissions/v261_v14_rf_rank_90_10.csv
```

Before saving each final submission, the notebook checks:

- the number of predictions matches the test set
- the output columns are exactly `index` and `target`
- no prediction values are missing
- prediction values fall between 0 and 1
- test-row ordering is preserved

## Validation Strategy

Because the supplied training observations cover time steps 1–35 while the test observations cover the later time steps 36–49, model evaluation uses chronological rather than random validation.

The primary expanding-window validation scheme is:

```text
Train 1–14  → Validate 15–21
Train 1–21  → Validate 22–28
Train 1–28  → Validate 29–35
```

A longer-horizon robustness check is also used:

```text
Train 1–14  → Validate 15–28
Train 1–21  → Validate 22–35
```

ROC-AUC is used as the evaluation metric throughout.

## Reproducibility Notes

- Competition data are intentionally excluded from GitHub and must be supplied separately.
- Generated prediction files are excluded from GitHub and are recreated by the notebook.
- File loading uses relative paths rather than machine-specific paths.
- Required package versions are pinned in `requirements.txt`.
- The selected final LightGBM and Random Forest models use `random_state=42`.
- The final notebook is intended to be executed from a fresh kernel from top to bottom.