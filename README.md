# Crop Diversity Forecasting

This repository contains a Google Colab-compatible Python notebook for one-year-ahead forecasting of Brazilian municipal crop diversity. The target variable is the Simpson Diversity Index (SDI), computed from planted or harvest-designated crop areas reported in IBGE/SIDRA Table 5457.

## Workflow

The notebook performs the complete modeling pipeline:

1. detects the input directory automatically;
2. reads and audits the four SIDRA spreadsheets;
3. harmonizes crop categories and avoids overlapping products;
4. computes crop shares, SDI, CR1, CR4, crop counts, registered crop area, and temporal features;
5. builds basic and expanded feature sets;
6. applies a chronological training, validation, and test design;
7. compares Persistence, Ridge regression, Random Forest, XGBoost, and multilayer perceptron models;
8. generates training and validation curves, test metrics, predictions, and model-interpretation outputs.

## Repository contents

```text
crop_diversity_forecasting_complete_colab_compliant.ipynb
README.md
.gitignore
tabela5457-2000-2006.xlsx
tabela5457-2007-2013.xlsx
tabela5457-2014-2020.xlsx
tabela5457-2021-2024.xlsx
```

The four spreadsheets are public input data from IBGE/SIDRA Table 5457 and may be versioned with the repository. Generated outputs, local environments, credentials, tokens, and private configuration files must not be committed.

## Data treatment

SIDRA symbols are handled as follows:

- `-`: zero;
- `...`: missing;
- `X`: missing;
- blank cells: missing;
- unexpected nonnumeric values: missing.

A municipality-year is retained only when the IBGE-reported total registered area is available and greater than zero. The term `registered crop area` is used because successive crops may occupy the same physical area within a year.

## Modeling design

The supervised task maps information available in year `t` to the SDI observed in year `t + 1`.

The common modeling sample starts in 2001 because 1999 data are not available for constructing lagged variables for the 2000 transition. The chronological split is:

| Subset | Feature years | Target years |
|---|---:|---:|
| Training | 2001-2013 | 2002-2014 |
| Validation | 2014-2018 | 2015-2019 |
| Test | 2019-2023 | 2020-2024 |

Hyperparameters are selected only with the training and validation periods. Final test results are produced once on the held-out test period.

## Running the notebook

1. Open `crop_diversity_forecasting_complete_colab_compliant.ipynb` in Google Colab or Jupyter.
2. Keep the four required `.xlsx` files in the same folder as the notebook.
3. Run all cells in order.
4. After completion, the workflow writes `sdi_ml_results.zip` to the input folder.

Intermediate files are created under `/content/sdi_ml_results` during execution and removed after the final archive is created.

## Outputs

The final archive contains:

- `tables/*.csv`: audits, validation results, test metrics, and interpretation tables;
- `figures/*.png`: annual summaries, learning curves, test diagnostics, and importance plots;
- `test_predictions.parquet`: test-period predictions;
- `run_metadata.json`: random seed, source files, selected configurations, feature sets, and software versions.

Generated outputs are ignored by Git and should not be committed.

## Reproducibility

The notebook fixes the random seed at `42` and records the software versions used in `run_metadata.json`. Required packages are checked or installed inside the notebook when needed.

Main Python dependencies:

```text
numpy
pandas
matplotlib
scikit-learn
xgboost
tensorflow
openpyxl
pyarrow
```

## Repository hygiene

Version only the notebook, documentation, license files, source code, and the four public IBGE input spreadsheets. Do not commit generated archives, exported figures or tables, prediction files, local environments, notebook checkpoints, credentials, tokens, or private configuration files.

Before committing, clear notebook outputs or use an output-stripping tool to avoid storing generated results inside the `.ipynb` file.
