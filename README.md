# Deases-pridiction-
Disease Prediction System

Predicts the likelihood of chronic illnesses — heart disease and diabetes —
from electronic health record (EHR)-style patient data, using a Random Forest
classifier. Includes a full ML pipeline (preprocessing → training → evaluation)
plus a Flask web app for interactive predictions.



⚠️ This project ships with synthetically generated data (see "About the
data" below) so it runs immediately with no downloads. Swap in a real
dataset before drawing any real conclusions — see instructions at the bottom.




Project structure

disease_prediction_system/
├── data/
│   ├── generate_data.py       # generates synthetic heart_disease.csv & diabetes.csv
│   ├── heart_disease.csv      # generated dataset (created after running the script)
│   └── diabetes.csv
├── src/
│   ├── preprocess.py          # shared imputation + scaling pipeline
│   ├── train_model.py         # trains & evaluates Random Forest models
│   └── predict.py             # CLI prediction on a single patient record
├── models/                    # saved .joblib model artifacts (after training)
├── outputs/                   # confusion matrix, ROC curve, feature importance plots
├── templates/
│   ├── index.html             # landing page
│   └── form.html              # prediction form + result UI
├── app.py                     # Flask web app (form UI + JSON API)
├── requirements.txt
└── README.md

Setup

python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt

Usage

1. Generate the dataset

python data/generate_data.py

Creates data/heart_disease.csv and data/diabetes.csv (1,000 rows each).


2. Train the models

cd src
python train_model.py --disease both     # or --disease heart / --disease diabetes

This runs a RandomizedSearchCV hyperparameter search with 5-fold cross-validation,
evaluates on a held-out test set, and saves:



Trained model → models/<disease>_rf.joblib

Confusion matrix, ROC curve, feature importance plots → outputs/


3. Predict from the command line

python predict.py --disease heart
python predict.py --disease diabetes

4. Run the web app

cd ..   # back to project root
python app.py

Open http://localhost:5000 in your browser. Fill out the form for heart
disease or diabetes and get an instant risk prediction with probability.


5. Use the JSON API

curl -X POST http://localhost:5000/api/predict/heart \
  -H "Content-Type: application/json" \
  -d '{"age":61,"sex":1,"chest_pain_type":0,"resting_bp":148,"cholesterol":275,
       "fasting_bs":1,"max_hr":118,"exercise_angina":1,"oldpeak":2.4,"st_slope":2}'


Model details

Algorithm	Random Forest (sklearn.ensemble.RandomForestClassifier)
Preprocessing	Median imputation for missing values, standard scaling
Class imbalance	class_weight="balanced"
Hyperparameter search	RandomizedSearchCV, 25 iterations, 5-fold stratified CV, optimized for ROC-AUC
Evaluation metrics	Accuracy, Precision, Recall, F1, ROC-AUC, Confusion Matrix
Explainability	Feature importance plots saved to outputs/

Heart disease features

age, sex, chest_pain_type, resting_bp, cholesterol, fasting_bs, max_hr, exercise_angina, oldpeak, st_slope


Diabetes features

pregnancies, glucose, blood_pressure, skin_thickness, insulin, bmi, diabetes_pedigree, age



About the data

The bundled generate_data.py script creates synthetic data with realistic
feature ranges and clinically-plausible correlations (e.g., higher age/cholesterol/BP
increases heart disease probability; higher glucose/BMI increases diabetes
probability), so the pipeline is runnable end-to-end without external downloads.


To use real data, replace the CSVs in data/ with:



UCI Heart Disease Dataset

Pima Indians Diabetes Dataset

Or real EHR data (e.g., MIMIC-III/IV, with proper institutional access/approval)


Just make sure the column names match what train_model.py expects (or update
the DISEASE_CONFIG / FIELD_CONFIG dictionaries in train_model.py / app.py).


Next steps / extensions


Add SHAP values for per-prediction explainability

Compare Random Forest against Logistic Regression / XGBoost / Gradient Boosting

Add authentication + patient record storage (e.g., PostgreSQL) for a real deployment

Add model monitoring / drift detection if used in production


Disclaimer

This is an educational/demonstration project. It is not a certified medical
device and should not be used for real clinical decision-making.


