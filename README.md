# Weather Prediction for Hyderabad (Rain and Temperature Forecasting)

Machine learning pipeline that predicts **(1) whether it will rain tomorrow** and **(2) tomorrow's maximum temperature** for Hyderabad, India, using 11 years (2015-2025) of daily weather data from the free [Open-Meteo](https://open-meteo.com/) archive API.

## Why this project
- Full data science workflow: data collection, cleaning, EDA, feature engineering, modelling, evaluation, interpretation
- Uses a **time-based train/test split** and **persistence baselines** to avoid the common mistakes of leakage and inflated scores
- Reproducible: one notebook, pinned dependencies, cached data

## Results
> Fill this table in with your real numbers after running the notebook. Do not leave placeholders in the published version.

| Task | Baseline (persistence) | Best model | Metric |
|------|------------------------|------------|--------|
| Rain tomorrow (classification) | _F1 = ?_ | _Model = ?, F1 = ?_ | F1, ROC-AUC |
| Tomorrow's max temp (regression) | _MAE = ? C_ | _Model = ?, MAE = ? C_ | MAE, RMSE, R2 |

Add your two best figures here, for example:
`![Feature importance](outputs/05_feature_importance.png)`

## Methodology
1. **Data:** hourly temperature, humidity, precipitation, pressure, wind, cloud cover, aggregated to daily
2. **Features:** current values, 1-3 day lags, 3 and 7 day rolling means, pressure change, seasonal sin/cos terms
3. **Targets:** rain tomorrow (>= 1 mm) and tomorrow's max temperature
4. **Split:** train 2015-2022, test 2023-2025 (chronological, no shuffling)
5. **Models:** Logistic/Ridge Regression, Random Forest, Gradient Boosting; compared against a persistence baseline
6. **Evaluation:** accuracy, precision, recall, F1, ROC-AUC, confusion matrix, time-series cross-validation (classification); MAE, RMSE, R2 (regression)

## Run it
**Option A: Google Colab (easiest)**
1. Open `notebooks/weather_prediction.ipynb` in Colab (upload it, or use "Open in Colab" from GitHub)
2. Runtime > Run all

**Option B: locally**
```bash
git clone https://github.com/<your-username>/weather-prediction.git
cd weather-prediction
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook notebooks/weather_prediction.ipynb
```
Run the notebook from inside the `notebooks/` folder or adjust paths; outputs are written to `outputs/` and `models/`.

## Repository structure
```
weather-prediction/
  notebooks/weather_prediction.ipynb   main analysis
  data/                                cached downloaded data (git-ignored)
  outputs/                             figures and result tables
  models/                              saved models (git-ignored)
  requirements.txt
  README.md
  NOTES.md                             study notes
```

## Limitations
- Single location; no satellite, radar or upper-air data
- Daily resolution; short-lived convective storms are hard to catch
- Models learn from past patterns; they are not physics-based forecasts and do not replace IMD or numerical weather prediction

## Future work
- Add neighbouring-city data and spatial features
- Try sequence models (LSTM, temporal convolution) and compare with tree models
- Probability calibration for rain forecasts
- Compare against Open-Meteo's own forecast model
- Deploy a small Streamlit app

## Data source and licence
Weather data from Open-Meteo (CC BY 4.0 for non-commercial use; check their terms and cite them). Code released under the MIT licence.
