# 🌿 PCEV: Car CO2 Emission Inspection Predictor

A Flask web app that predicts a car's CO2 emissions with XGBoost and flags vehicles that should get an engine inspection.

![Python](https://img.shields.io/badge/Python-3.9-3776AB?logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-2.1-000000?logo=flask&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-1.6-337AB7)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.1-F7931E?logo=scikitlearn&logoColor=white)
![pandas](https://img.shields.io/badge/pandas-150458?logo=pandas&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?logo=jupyter&logoColor=white)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?logo=html5&logoColor=white)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?logo=css3&logoColor=white)

## Overview

Team project by Group 7 for a Decision Support Systems course (June 2022). The user enters four vehicle specifications; the app predicts CO2 emissions in g/km and, when the prediction is above **200 g/km**, adds a recommendation (in Indonesian) that the vehicle should have its engine inspected. A simpler single-author version of the same idea lives in [co2emision-DSS](https://github.com/gerritezra/co2emision-DSS).

## Dataset

Canadian vehicle fuel consumption and CO2 emissions data: 7,385 rows and 12 columns, loaded in the notebook from a Google Drive link.

Columns: `Make`, `Model`, `Vehicle Class`, `Engine Size(L)`, `Cylinders`, `Transmission`, `Fuel Type`, `Fuel Consumption City (L/100 km)`, `Fuel Consumption Hwy (L/100 km)`, `Fuel Consumption Comb (L/100 km)`, `Fuel Consumption Comb (mpg)`, `CO2 Emissions(g/km)`.

## Approach

From `Emisi_CO2.ipynb`:

1. Select `Engine Size(L)`, `Cylinders`, `Transmission` and `Fuel Consumption Comb (mpg)` as features, `CO2 Emissions(g/km)` as the target.
2. Strip the gear count from `Transmission` codes (`A6` becomes `A`), keep only plain automatic (`A`) and manual (`M`), and label-encode them (A = 0, M = 1). 3,300 rows remain.
3. Check correlations with the target and rank feature importance with an `ExtraTreesRegressor`.
4. Evaluate `XGBRegressor` with 5-fold cross-validation (MSE and MAE).
5. Fit on a 70/30 train/test split, plot predicted vs. actual values, and save the model to `Emisi_XGB_model.pkl`.

`main.py` loads the saved model, serves the form at `GET /`, and handles `POST /predict`.

## Results

From the notebook outputs:

| Metric (5-fold CV, XGBRegressor) | Value | Cell |
| --- | --- | --- |
| Mean RMSE | 9.39 g/km | 21 |
| Mean MAE | 4.45 g/km (folds: 4.27, 4.35, 4.89, 4.70, 4.04) | 20 |

Correlation with CO2 emissions (cell 15): fuel consumption in mpg -0.91, engine size 0.86, cylinders 0.83, transmission -0.27.

## Tech stack

- **Model:** XGBoost, scikit-learn, pandas, NumPy, Matplotlib, seaborn (Jupyter)
- **Back end:** Flask, served by Gunicorn
- **Front end:** HTML and CSS (Jinja2 template)
- **Deployment:** `Procfile` for Heroku; GitHub Actions workflows for Azure Web App

## Project structure

```
PCEV-DSS-Group7/
├── .github/workflows/
│   ├── main_dss-group7-pcev.yml     # deploy to Azure Web App
│   └── main_pcev-dss-group7.yml     # deploy to Azure Web App
├── static/
│   ├── css/style.css
│   └── index/assets/images/         # page backgrounds and buttons
├── templates/index.html             # input form, prediction and recommendation
├── Emisi_CO2.ipynb                  # data prep, evaluation, model export
├── Emisi_XGB_model.pkl              # trained XGBoost model
├── main.py                          # Flask app
├── requirements.txt
├── Procfile
└── README.md
```

## How to run

```bash
python -m venv venv
source venv/bin/activate          # Windows: venv\Scripts\activate
pip install -r requirements.txt
python main.py                    # http://127.0.0.1:5000
```

Form inputs, in order: engine size (L), cylinders, transmission (1 = manual, 0 = automatic), combined fuel consumption (mpg).

To serve it with Gunicorn (as the `Procfile` does on Heroku):

```bash
gunicorn main:app
```

## Author

Built by Group 7. Repository maintained by **Gerrit Ezra Yudi Kairupan**.

- Portfolio: [gerritezra.my.id](https://gerritezra.my.id)
- GitHub: [gerritezra](https://github.com/gerritezra)
