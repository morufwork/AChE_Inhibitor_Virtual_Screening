# AChE Inhibitor Virtual Screening

This repository contains a cheminformatics and machine-learning workflow for identifying potential acetylcholinesterase (AChE) inhibitors. It combines curated ChEMBL, PubChem, literature, orthologous species, and ZINC screening data with RDKit molecular fingerprints and classical machine-learning models.

The workflow covers data cleaning and standardization, duplicate/overlap checks, fingerprint-based model comparison, calibrated Random Forest validation, and virtual screening of a ZINC subset.

## Repository Contents

```text
.
├── dataset/
│   ├── chembl_data/          # ChEMBL AChE bioactivity data and standardized SMILES labels
│   ├── data_literature/      # Human and eel literature validation sets, SDF files, and relabeled CSVs
│   ├── orthologous/          # Cow, mouse, rat, and ray orthologous AChE datasets
│   ├── pubchem data/         # PubChem raw, merged, and standardized AChE data
│   └── zinc/                 # ZINC compounds prepared for virtual screening
├── result/                   # Cross-validation metrics, MCDM rankings, and ZINC predictions
├── *.ipynb                   # Data processing, modeling, validation, and screening notebooks
└── RandomForest_PlattCalibrated.joblib
```

## Data Summary

| File or folder | Description |
| --- | --- |
| `dataset/chembl_data/acetylcholinesterase_bioactivity_data_preprocessed1.csv` | Preprocessed ChEMBL AChE bioactivity records with ChEMBL IDs, canonical SMILES, and activity values. |
| `dataset/chembl_data/final_standard_smiles.csv` | Standardized ChEMBL SMILES with binary activity classes. |
| `dataset/pubchem data/` | PubChem assay records, merged data, and standardized SMILES/class labels. |
| `dataset/data_literature/` | Human and eel literature test sets in SDF and CSV form, including relabeled clean files. |
| `dataset/orthologous/` | Orthologous AChE datasets for cow, mouse, rat, and ray, including cleaned versions. |
| `dataset/zinc/standardized_zinc.csv` | Standardized ZINC SMILES used for virtual screening. |
| `dataset/zinc/standardized_zinc_with_ids.csv` | Standardized ZINC SMILES with generated ZINC IDs used to join screening outputs. |
| `result/` | Model metrics, fingerprint rankings, and prediction outputs. |

## Notebooks

| Notebook | Purpose |
| --- | --- |
| `data_process_external.ipynb` | Standardizes external compound SMILES using RDKit molecule normalization, salt removal, and canonicalization. |
| `deduplication_overlapping.ipynb` | Checks overlap and duplicate compounds between datasets using canonicalized SMILES. |
| `atompair.ipynb` | Trains and evaluates machine-learning models with Atom Pair fingerprints. |
| `avalon.ipynb` | Trains and evaluates machine-learning models with Avalon fingerprints. |
| `classical_ECFP4.ipynb` | Trains and evaluates machine-learning models with ECFP4 fingerprints. |
| `macc.ipynb` | Trains and evaluates machine-learning models with MACCS keys. |
| `morgan.ipynb` | Trains and evaluates machine-learning models with Morgan/count fingerprints. |
| `rdk.ipynb` | Trains and evaluates machine-learning models with RDKit fingerprints. |
| `topological.ipynb` | Trains and evaluates machine-learning models with topological fingerprints. |
| `calibrated_model_descriptor_external_validation.ipynb` | Loads the calibrated Random Forest model and validates it on external descriptor/fingerprint datasets. |
| `validation.ipynb` | Runs external validation metrics for saved models and validation datasets. |
| `linpski.ipynb` | Computes Lipinski rule-of-five descriptors for selected compounds. |
| `AD.ipynb` | Computes a Morgan-fingerprint applicability domain for prioritized ZINC hits. |

## Modeling Approach

The modeling notebooks compare multiple classifiers under stratified 10-fold cross-validation with class weighting:

- LightGBM
- Random Forest
- Extra Trees
- Support Vector Machine
- Gradient Boosting
- Logistic Regression
- AdaBoost
- K-Nearest Neighbors
- Naive Bayes
- Decision Tree

The reported metrics include accuracy, precision, recall, F1 score, ROC-AUC, PR-AUC, Matthews correlation coefficient, enrichment factors, and BEDROC.

## Results

The `result/` folder contains fingerprint-specific cross-validation summaries:

- `chembl_atompair_classweighted_10fold_metrics.csv`
- `chembl_avalon_classweighted_10fold_metrics.csv`
- `chembl_ecfp4_classweighted_10fold_metrics.csv`
- `chembl_macc_classweighted_10fold_metrics.csv`
- `chembl_morgan_classweighted_10fold_metrics.csv`
- `chembl_rdk_classweighted_10fold_metrics.csv`
- `chembl_topo_classweighted_10fold_metrics.csv`

It also includes multi-criteria decision-making rankings:

- `mcdm_fingerprint_ranking_RF.csv`
- `mcdm_fingerprint_ranking_ET.csv`
- `mcdm_fingerprint_ranking_GBM.csv`

The ZINC virtual screening outputs are:

- `alt_ZINC_ChEMBL_RF_Morgan_predictions.csv`
- `alt_ZINC_ChEMBL_RF_Morgan_high_confidence.csv`
- `zinc_hit_AD.csv`

In the included result files, Random Forest and Extra Trees models with Morgan/ECFP-style fingerprints are among the strongest performers across ROC-AUC, PR-AUC, F1, and MCC.

## Trained Model

`RandomForest_PlattCalibrated.joblib` is the saved calibrated Random Forest model. It is approximately 535 MB, so it must be tracked with Git LFS when pushing to GitHub.

After cloning the repository, install Git LFS and pull the model file:

```bash
git lfs install
git lfs pull
```

## Environment

The notebooks use Python with common cheminformatics and machine-learning packages:

- `rdkit`
- `numpy`
- `pandas`
- `scikit-learn`
- `lightgbm`
- `joblib`
- `matplotlib`
- `openpyxl` or another Excel reader/writer for `.xlsx` result files

A typical Conda environment can be created with:

```bash
conda create -n ache-vs python=3.10 rdkit numpy pandas scikit-learn lightgbm joblib matplotlib openpyxl -c conda-forge
conda activate ache-vs
```

## Basic Usage

1. Open the notebooks in JupyterLab or Jupyter Notebook.
2. Run `data_process_external.ipynb` to standardize external datasets when needed.
3. Run the fingerprint notebooks to reproduce cross-validation comparisons.
4. Use `calibrated_model_descriptor_external_validation.ipynb` or `validation.ipynb` for external validation.
5. Review the generated metrics and screening outputs in `result/`.

## Notes

- Several notebooks contain local paths or historical filenames from the analysis process. Check input paths before rerunning notebooks in a new environment.
- The large `.joblib` model requires Git LFS for GitHub hosting.
- The files in `result/` are included as generated outputs for reproducibility and inspection.
