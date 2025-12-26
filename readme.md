## Repository Structure

.
├── README.md
├── feature_matrix.csv
├── notebooks/
│   ├── ml_pipeline.ipynb
│   └── evaluation_auc.ipynb

## How to Run
Get feaure matrix from "https://drive.google.com/file/d/1BUTy7znc8__bnm1NFPZrtpNW2Hxkp2nl/view?usp=sharing"
1. Clone this repository
2. Install required Python dependencies
3. Run the notebooks in the `notebooks/` directory in order



# Multi-cancer DNA Methylation Classification using DMR Features

This repository implements a machine learning pipeline for classifying multiple cancer types using Differentially Methylated Region (DMR)–level DNA methylation features.  
The workflow follows the methodological framework of the reference research paper and focuses on sample-level (GSM-level) analysis.


1️. Project Starting Point

The analysis in this repository starts from a precomputed feature matrix:

- `feature_matrix.csv`

This file serves as the primary input for all machine learning experiments in this project.

2. Feature Matrix Description

The file `feature_matrix.csv` is a tabular dataset constructed at the sample (GSM) level.

- Each **row** represents one methylation sample (GSM-level)
- Each **column** represents a Differentially Methylated Region (DMR) feature
- Feature values correspond to aggregated methylation levels within each DMR

The matrix also contains the following metadata columns:

- `gsm_id`: unique identifier for each methylation sample
- `tissue`: cancer or tissue label used as the target variable

The feature matrix contains approximately:
- ~300 samples
- ~1800 DMR features
- 7 tissue classes: CRC, HCC, ESCC, GC, EAC, PDAC, and Normal

3. Sample-Level Analysis and Patient Mapping

All analyses in this repository are performed at the **sample (GSM) level**.

Although patient-level clinical data is available in the supplementary files of the original study, explicit mappings between GSM identifiers and patient identifiers are not provided in the released metadata. Therefore, patient-level aggregation cannot be performed without introducing assumptions.

As a result, all machine learning models are trained and evaluated at the sample level, which is consistent with both the structure of the available data and the methodology of the reference research paper.

4. Data Download and Availability

This repository does not host raw methylation sequencing files due to size constraints.

### Required Data
To reproduce the machine learning analysis in this repository, the following file is required:

- `feature_matrix.csv`

This file contains all DMR-level features and class labels needed to run the notebooks.
### Original Raw Data Source (Informational)

The feature matrix was originally derived from publicly available DNA methylation data deposited in the Gene Expression Omnibus (GEO).

At a high level, the original data preparation involved:
1. Downloading GSM-level methylation files from GEO
2. Extracting methylation ratios from alignment output files
3. Mapping CpG sites to predefined Differentially Methylated Regions (DMRs)
4. Aggregating CpG methylation values into DMR-level features per sample

These preprocessing steps are described for transparency but are **not required** to reproduce the machine learning experiments in this repository.


5. Preprocessing

The following preprocessing steps were applied before model training:

1. The feature matrix was loaded from CSV.
2. Features (`X`) and labels (`y`) were separated:
   - Features: all DMR columns
   - Labels: `tissue`
3. The dataset was split into training (80%) and test (20%) sets using stratified sampling to preserve class distributions.
4. Missing values were handled using median imputation:
   - The imputer was fitted on the training set only
   - The same transformation was applied to the test set to avoid data leakage

6. Feature Selection

Supervised feature selection was performed using the ANOVA F-test.

- Feature selection was applied only on the training set
- The top 300 most informative DMR features were selected
- The same feature subset was applied to the test set

This step reduces dimensionality and focuses the models on biologically informative regions.

7. Machine Learning Models

Two classical machine learning models were evaluated:

1. Random Forest classifier
2. Linear Support Vector Machine (SVM)

Both models were trained using identical training/test splits and the same selected feature set.

8. Evaluation Metrics

Model performance was evaluated using the following metrics:

- Accuracy
- Precision, Recall, and F1-score
- Confusion Matrix
- ROC–AUC (One-vs-Rest):
  - Per-cancer ROC–AUC
  - Macro-averaged ROC–AUC (pan-cancer performance)

9. Key Results Summary

- CRC, HCC, and ESCC achieved the highest ROC–AUC values
- GC and PDAC showed intermediate performance
- EAC showed the lowest ROC–AUC, consistent with the reference paper
- The macro-averaged ROC–AUC was comparable to the paper’s reported pan-cancer performance
