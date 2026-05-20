# DoorDash Delivery Time Prediction

Predicting DoorDash delivery duration (in seconds) using XGBoost regression on order, dasher availability, and restaurant features.


## 🎯 Problem Statement
Accurate delivery time estimates are critical to customer satisfaction on food delivery platforms. When ETAs are off, customers lose trust and churn. DoorDash generates rich operational data on every order — dasher availability, restaurant prep estimates, order size, driving distance — but translating that into a reliable predicted delivery time is non-trivial.
This project asks: can we use order-level and market-level features to accurately predict how long a DoorDash delivery will take from order placement to doorstep?

## 🗃️ Dataset Source
SourceFileDoorDash historical order datadatasets/historical_data.csv
Key raw columns include:

created_at, actual_delivery_time — used to derive the target variable
estimated_order_place_duration — estimated restaurant prep time
estimated_store_to_consumer_driving_duration — estimated drive time
total_onshift_dashers, total_busy_dashers — dasher market conditions
total_items, num_distinct_items — order size
store_id, store_primary_category — restaurant identity and cuisine type


## 🛠️ Approach
Feature Engineering

Target variable: delivery_time = actual_delivery_time - created_at (total seconds end-to-end)
non_prep_time: derived as estimated_order_place_duration + estimated_store_to_consumer_driving_duration
percent_busy_dashers: derived as total_busy_dashers / total_onshift_dashers to capture market saturation; infinite values (division by zero) replaced with 0
store_primary_category: missing values imputed per store_id using that store's most frequent category, then One-Hot Encoded

Preprocessing

delivery_time NaN rows dropped (no target = unusable)
total_onshift_dashers and total_busy_dashers missing values imputed with median via SimpleImputer
Correlation analysis revealed total_items and non_prep_time were highly correlated with other features and dropped to reduce multicollinearity

Final Feature Set
FeatureTypeDescriptionnum_distinct_itemsNumericNumber of unique items in the orderestimated_order_place_durationNumericEstimated restaurant prep time (seconds)estimated_store_to_consumer_driving_durationNumericEstimated drive time (seconds)percent_busy_dashersNumeric (derived)Fraction of on-shift dashers currently busycat_*Binary (OHE)Restaurant cuisine/category dummies
Model
XGBoost Regressor with the following configuration:

n_estimators = 100
learning_rate = 0.1
max_depth = 5
70/30 train-test split (random_state=42)

Evaluated on:

MAE (Mean Absolute Error) — average prediction error in seconds
RMSE (Root Mean Squared Error) — penalizes larger errors more heavily

PCA Analysis
PCA was applied post-modeling to visualize cumulative explained variance across the ~80+ features (after OHE expansion), confirming the relative contribution of the feature set.

## 📊 Key Findings

estimated_store_to_consumer_driving_duration and estimated_order_place_duration were the top predictive features — DoorDash's own internal estimates are the strongest signal for actual delivery time.
percent_busy_dashers captures market congestion and contributed meaningfully to the model, confirming that dasher availability at order time impacts delivery speed.
Restaurant category (OHE) added modest but measurable signal — some cuisine types (e.g., fast food vs. full-service) have systematically different prep time profiles.
total_items and non_prep_time were dropped due to high correlation with other retained features — keeping them would have introduced redundancy without adding predictive value.

Model Performance
MetricRaw ValueHuman-ReadableMAE874.08 seconds~14.6 minutesMSE537,924,965 s²—
The model predicts delivery time within roughly 14.6 minutes on average. Note: the notebook labels the second metric as RMSE but sklearn's mean_squared_error returns MSE by default (units are seconds²) — the printed value is not true RMSE. To get RMSE, use mean_squared_error(..., squared=False) or math.sqrt(mse), which yields ~386 seconds (~6.4 minutes).

Repository Structure
DoorDashDeliveryPrediction/
├── code.ipynb        # Full analysis: EDA, feature engineering, modeling, evaluation
└── datasets.zip      # Historical DoorDash order data (unzip before running)

## 🚀 Getting Started
bash# 1. Clone the repo
git clone https://github.com/alexjbyoon/DoorDashDeliveryPrediction.git
cd DoorDashDeliveryPrediction

# 2. Install dependencies
pip install pandas numpy matplotlib seaborn scikit-learn xgboost jupyter

# 3. Unzip the dataset
unzip datasets.zip

# 4. Launch the notebook
jupyter notebook code.ipynb

Requirements
PackagePurposepandasData loading and manipulationnumpyNumerical operationsmatplotlib / seabornVisualizationscikit-learnPreprocessing, imputation, train-test split, metricsxgboostXGBoost regression modeljupyterNotebook environment
Python 3.8+ recommended.
