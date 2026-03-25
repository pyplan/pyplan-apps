# PREBUILD DOCUMENTATION

## Prebuild - Credit Risk Analysis and Prediction

## 1. Overall purpose

This application implements an end-to-end **credit risk analysis and loan approval prediction** workflow.

It combines:

- data ingestion and preparation,
- exploratory analysis,
- machine learning training and evaluation,
- operational scoring for new loan applications.

---

## 2. Business process covered

1. **Ingest and clean historical credit data**.
2. **Explore and profile data** quality and relationships.
3. **Train classification models** on normalized and balanced datasets.
4. **Compare model performance** (Accuracy, Precision, Recall, ROC, AUC, Confusion Matrix).
5. **Score new loan applications** entered through a form.
6. **Suggest alternative credit amount** to improve approval likelihood.

---

## 3. Module hierarchy

```
_root_
├── Data
├── Indexes
├── Data Exploration and Analysis
├── Machine Learning - Training and Test
│   ├── Random Forest Method
│   ├── Logistic Regression Method
│   └── Gaussian Naive Bayes Method
└── Predictions
    └── Predictions Functions
```

---

## 4. Module-by-module detail

### 4.1 Data (`data_mod`)

**Goal:** centralize source data reading and base transformations.

**What it does:**

- Reads the original German Credit dataset.
- Builds a cleaned/enriched version with derived fields.
- Fills missing account-related values.
- Generates overview reports before and after manipulation.

**Role in the pipeline:**

Provides the **trusted base dataset** for analytics and modeling.

---

### 4.2 Indexes (`indexes_mod`)

**Goal:** define shared indexes/domains and global parameters.

**What it does:**

- Stores index definitions for numeric and categorical columns.
- Defines category domains (sex, housing, purpose, account types, etc.).
- Defines form column structure for new loans.
- Exposes reproducibility/control parameters (e.g., fixed random state).

**Role in the pipeline:**

Provides **semantic standardization** and reproducibility.

---

### 4.3 Data Exploration and Analysis (`data_exploration_analysis_mod`)

**Goal:** profile data and prepare it for machine learning.

**What it does:**

- Builds `Normalized Data` by:
  - encoding categorical variables,
  - safely coercing to numeric types,
  - applying Min-Max normalization.
- Produces descriptive profiling (`Data info`).
- Computes and visualizes Pearson correlations (`Correlations`).

**Role in the pipeline:**

Delivers a **model-ready dataset** plus analytical understanding.

---

### 4.4 Machine Learning - Training and Test (`machine_learning_training_and_test_mod`)

**Goal:** create train/test datasets and orchestrate model comparison.

**What it does:**

- Builds features and target (`X Variable`, `Y Variable`).
- Performs stratified train/test split with configurable test size.
- Applies SMOTE to the training set to mitigate class imbalance.
- Exposes `X Train`, `Y Train`, `X Test`, `Y Test`.
- Consolidates performance comparison across methods.

**Role in the pipeline:**

Acts as the **core training and evaluation orchestrator**.

---

### 4.5 Random Forest Method (`random_forest_method_mod`)

**Goal:** train and evaluate a Random Forest classifier.

**What it does:**

- Defines RF hyperparameters.
- Trains the model on balanced training data.
- Generates class predictions and probabilities on test data.
- Computes:
  - Accuracy
  - Precision
  - Recall
  - Confusion Matrix
  - ROC Curve
  - ROC AUC
- Publishes feature importance ranking.

**Role in the pipeline:**

Captures non-linear patterns and feature interactions.

---

### 4.6 Logistic Regression Method (`logistic_regression_method_mod`)

**Goal:** train and evaluate a Logistic Regression classifier.

**What it does:**

- Configures regularization parameters.
- Trains a linear probabilistic model.
- Produces predictions and probabilities.
- Computes:
  - Accuracy
  - Precision
  - Recall
  - Confusion Matrix
  - ROC Curve
  - ROC AUC

**Role in the pipeline:**

Provides an interpretable and robust binary classification baseline.

---

### 4.7 Gaussian Naive Bayes Method (`gaussian_naive_bayes_method_mod`)

**Goal:** train and evaluate a Gaussian Naive Bayes classifier.

**What it does:**

- Trains the GNB model.
- Produces class and probability predictions on test data.
- Computes:
  - Accuracy
  - Precision
  - Recall
  - Confusion Matrix
  - ROC Curve
  - ROC AUC

**Role in the pipeline:**

A lightweight, fast benchmark model for comparison.

---

### 4.8 Predictions (`predictions_mod`)

**Goal:** enable operational scoring for new loan requests.

**What it does:**

- Hosts `New Loans` input form.
- Delegates inference logic to the `Predictions Functions` submodule.

**Role in the pipeline:**

Bridges trained models with real operational usage.

---

### 4.9 Predictions Functions (`predictions_functions_mod`)

**Goal:** execute prediction and recommendation logic for new loans.

**What it does:**

- Validates complete form rows (`Get completed loans`).
- Preprocesses new-loan records to match training schema.
- Normalizes and aligns features (dummy columns + scaling).
- Runs model-agnostic prediction wrapper.
- Exposes public scoring functions per model:
  - Random Forest
  - Logistic Regression
  - Gaussian Naive Bayes
- Computes **suggested credit amount** per model to potentially convert rejected cases into approved ones.

**Role in the pipeline:**

Implements the **production inference and decision-support layer**.

---

## 5. End-to-end flow summary

```
Data + Indexes
   ↓
Data Exploration and Analysis (normalization + correlations)
   ↓
Machine Learning - Training and Test (split + SMOTE)
   ↓
Model modules (RF / LR / GNB) + metrics
   ↓
Predictions (form + inference functions)
   ↓
Risk prediction + suggested credit amount
```

---

## 6. Functional outcome

The app turns historical credit data into actionable decisions for new applications by providing:

- a traceable modeling pipeline,
- objective model comparison,
- operational outputs (risk prediction + recommended credit amount).

## 7. Application interfaces

- “Menu” (`448be3d7-aee9-4af9-aece-187d9b94537a`) interface consists of a single full-width core.Menu component, indicating it is a navigation-focused screen intended to present a menu for accessing other parts of the application.
- “Analysis 1” (`92be1e59-bb35-4c82-a10c-4dbc97ec2877`) is an analysis dashboard focused on a single dataset node titled “Data (with new columns and loans),” presenting the underlying data in a large table view alongside six chart visualizations derived from the same dataset. The layout places the table on the left and arranges the charts in a grid on the right (top, middle, and bottom rows), enabling users to inspect the enriched loan data at record level while simultaneously viewing multiple graphical perspectives of that same data.
- “Analysis 2” (`f602754d-ddec-447b-b455-6cf93b49213d`) is a dashboard-style interface that presents a single underlying dataset node, “Data (with new columns and loans),” through multiple chart components. It contains eight separate chart views (each a core.Chart instance) arranged in a grid across the page—three charts in the top row, two wider charts in the middle row, and three charts in the bottom row—providing several simultaneous visual perspectives on the same enriched loans dataset for exploratory analysis.
- “Analysis 3” (`c931632c-38b3-4a42-a3a3-c603693b4e9f`) is a dashboard-style interface composed entirely of multiple chart components (arranged in a grid) that all reference the same underlying node titled “Data (with new columns and loans).” The interface appears designed to explore and visualize this enriched loans dataset through several different chart views: two larger charts at the top, followed by multiple smaller charts in rows beneath, and a final full-width chart at the bottom, enabling users to analyze the same prepared data from multiple perspectives within a single screen.
- The “Machine Learning Process and Results” (`68e3aff7-1473-43b3-abac-529c762c2715`) interface presents a side-by-side evaluation of three classification approaches—Random Forest, Logistic Regression, and Gaussian Naive Bayes—using a consistent set of performance outputs. For each method it displays key metric indicators (Accuracy, Precision Score, Recall, and ROC AUC), a confusion matrix table, and an interactive ROC curve chart (Plotly). In addition, it includes a Feature Importance table specifically for the Random Forest method, and uses HTML blocks at the top of each method’s column to visually separate or label the sections.
- The “New Loans Simulation” (`1093167d-ac39-4f77-b0c0-b7beaa971675`) interface is a single-screen dashboard centered on capturing or configuring inputs related to new loans: it contains one main component, a form titled “New Loans,” which is intended to let the user enter and adjust new-loan parameters within the broader Credit Risk Analysis and Prediction application.