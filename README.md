# CardioViz 3D - Cardiovascular Risk Visualization & Prediction
Multimodal AI Hackathon 2026, Track A (BioTech). Team: Chukka Sujeeva Bharathi

**Predictions are for decision support / educational purposes only and are NOT a substitute for formal diagnostic imaging or a doctor.**

## What it does
- Trains models on the UCI "Extension of Z-Alizadeh Sani" dataset (303 patients, 59 features) to predict overall CAD (`Cath`) and stenosis of the three coronary arteries: LAD, LCX and RCA.
- Shows an interactive 3D heart (Three.js) where the LAD, LCX and RCA arteries change color (green, yellow, red) with the predicted stenosis probability.
- Clinical dashboard: per-patient probabilities, the dataset's actual label, and the top 5 SHAP features showing why the model predicted that risk. You can rotate, zoom, and click an artery to see its explanation.

## Method
1. Inputs: the 55 demographic, symptom/exam, ECG, lab and echo features. `LAD`, `LCX`, `RCA` and `Cath` are removed from the inputs to prevent target leakage.
2. Text columns are one-hot encoded. Labels: Normal = 0, CAD/Stenotic = 1.
3. One Random Forest (300 trees, balanced class weights) per target.
4. Evaluation: 5-fold stratified cross-validation (out-of-fold predictions).
5. Explanations: SHAP TreeExplainer. The patient probabilities shown in the 3D page are the cross-validated (out-of-fold) predictions.

## Results (5-fold cross-validation)
| Target | Positive rate | Accuracy | Precision | Recall | F1 | ROC-AUC |
|---|---|---|---|---|---|---|
| Overall CAD (Cath) | 0.71 | 0.865 | 0.896 | 0.917 | 0.906 | 0.921 |
| LAD | 0.58 | 0.795 | 0.804 | 0.859 | 0.831 | 0.851 |
| LCX | 0.39 | 0.673 | 0.582 | 0.597 | 0.589 | 0.722 |
| RCA | 0.38 | 0.650 | 0.538 | 0.491 | 0.514 | 0.712 |

Overall CAD and LAD are predicted well. LCX and RCA are harder: the dataset has only 303 patients and no direct imaging data, so these two models are weaker. This is a limitation, not a clinical tool.

## Files
- `Biotech.ipynb` - data loading, training, evaluation, SHAP and export code
- `index_standalone.html` - the 3D web app (open directly in a browser, no server needed)
- `index.html`, `data.js` - non-standalone version of the same app
- `results.csv` - evaluation metrics
- `shap_Cath.png`, `shap_LAD.png`, `shap_LCX.png`, `shap_RCA.png` - feature importance charts
- `heart_models.joblib`, `X_processed.csv` - trained models and processed inputs

## Prerequisites
Python 3.10+, JupyterLab, and these packages: pandas, numpy, scikit-learn, shap, matplotlib, joblib, openpyxl.

## Setup and run
1. Install the packages:

        pip install pandas numpy scikit-learn shap matplotlib joblib openpyxl

2. Download the dataset from https://archive.ics.uci.edu/dataset/411 (extension of Z-Alizadeh Sani, CC BY 4.0), unzip it, and put the .xlsx file next to the notebook.
3. Open `Biotech.ipynb` in JupyterLab and run all cells. This trains the models, saves the SHAP charts, and writes `data.js`.
4. To view the app, double-click `index_standalone.html` (or `index.html`) to open it in Chrome or Edge. Pick a patient, rotate and zoom the heart, and click an artery to see its explanation.

## Dataset credit
Alizadehsani, R., Roshanzamir, M., Sani, Z. (2013). Extension of Z-Alizadeh Sani dataset. UCI Machine Learning Repository. https://doi.org/10.24432/C5461K
