# datathon-2025-lifeline-team76  

## Team ID: TM-76  

### Team Members  
- Vanessa Stefany Arifianto  
- Menezes Leisha Pritika  
- Alexandra Naomira Krisdianto  

---

## Project Overview  
This repository contains our submission for **Datathon 2025 – Lifeline**, which focuses on automated classification of fetal health states using **Cardiotocography (CTG)** data. The objective is to predict fetal state categories — *Normal, Suspect,* or *Pathologic* — from fetal heart rate (FHR) and uterine contraction (UC) features, providing an interpretable decision-support tool for clinicians.  

---

## Dataset  
- **Source:** [UCI Machine Learning Repository – Cardiotocography (CTG)](https://archive.ics.uci.edu/dataset/193/cardiotocography)  
- **Samples:** 2,126 records  
- **Target column:** `NSP`  
  - Original classes: 1 (Normal), 2 (Suspect), 3 (Pathologic)  
  - Mapped in this project to: 0 (Normal), 1 (Suspect), 2 (Pathologic)  
- **Features used:**  
  `LB, AC, FM, UC, ASTV, mSTV, ALTV, mLTV, DL, DS, DP, Width, Min, Max, Nmax, Nzeros, Mode, Mean, Median, Variance, Tendency`  

- **Class distribution:**

| Class | Count |
|-------|-------|
| 0 (Normal)   | 1655 |
| 1 (Suspect)  | 295  |
| 2 (Pathologic)| 176  |

---

## Data Processing Pipeline  
1. **Loading:** Iterates through all sheets in `CTG.xls` to locate the label column (`NSP` or `CLASS`) and normalize headers.  
2. **Cleaning:** Removes missing values and ensures all selected columns are numeric.  
3. **Transformation:** Encodes class labels {1, 2, 3} → {0, 1, 2} and applies `StandardScaler` to normalize features.  
4. **Balancing:** Addresses class imbalance using SMOTE within the cross-validation pipeline to prevent data leakage.  
5. **Splitting:** Stratified 80/20 train–test split with random state 42.  

---

## Model Design  

### Pipeline  
`StandardScaler → SMOTE → Classifier`  

### Models Implemented  
- Logistic Regression: `LogisticRegression(max_iter=1000, class_weight='balanced', random_state=42)`  
- Random Forest: `RandomForestClassifier(n_estimators=300, class_weight='balanced', random_state=42)`  

### Model Selection  
- **Method:** `GridSearchCV` applied to Random Forest  
- **Parameter Grid:**  
  - `n_estimators ∈ {200, 300, 500}`  
  - `max_depth ∈ {None, 6, 10}`  
  - `min_samples_split ∈ {2, 5}`  
- **Scoring Metric:** Macro-F1 (robust against class imbalance)  
- **Validation:** 5-fold `StratifiedKFold` cross-validation  

---

## Evaluation Metrics  

| Model | Macro-F1 | Balanced Accuracy |
|-------|-----------|-----------------|
| Logistic Regression | 0.787 | 0.850 |
| Random Forest | 0.899 | 0.906 |

**Random Forest (Best Hyperparameters)**  
`{'clf__max_depth': None, 'clf__min_samples_split': 5, 'clf__n_estimators': 200}`  
**CV Macro-F1:** 0.904  

**Hold-out Evaluation (Best RF):**  
- Balanced Accuracy: 0.899  
- Macro-F1: 0.884  

**Per-class performance:**

| Class | Precision | Recall | F1-score | Support |
|-------|-----------|--------|----------|--------|
| 0 (Normal)   | 0.97 | 0.96 | 0.97 | 332 |
| 1 (Suspect)  | 0.77 | 0.80 | 0.78 | 59  |
| 2 (Pathologic)| 0.87 | 0.94 | 0.90 | 35  |

---

## Explainability  
- **Feature Importances:** ASTV, ALTV, Mean, AC, Median, DP, Mode, LB, Variance, Width were most influential.  
- **SHAP Analysis:** Reduced variability and frequent decelerations increase likelihood of Pathologic classification, consistent with CTG guidelines (Ayres-de-Campos et al., 2000).  

*(Include a `reports/shap_summary.png` for a visual example if available.)*  

---

## How to Run  

```bash
# Clone the repository
git clone https://github.com/leishapm/datathon-2025-lifeline-team76.git
cd datathon-2025-lifeline-team76

# Install dependencies
pip install -r requirements.txt

# Run the project (notebook workflow)
jupyter notebook datathon76.ipynb
# or open it directly in VS Code or Colab
