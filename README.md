2. DoorDash Delivery Time Prediction
Repository: alexjbyoon/DoorDashDeliveryPrediction
Problem Statement
Accurate delivery time estimates are critical to customer satisfaction in food delivery. Late or inaccurate ETAs erode trust and increase churn. This project builds a model to predict DoorDash delivery durations based on order, restaurant, and market features.
Dataset Source
DoorDash delivery records provided as datasets.zip. Includes fields such as order creation time, restaurant preparation time, market/region, number of outstanding orders, and dasher availability.
Approach
Data cleaning and feature engineering on time-based and categorical variables.
Exploratory analysis to identify factors most correlated with delivery duration.
Regression models (linear regression, gradient boosting) trained to predict delivery time in minutes.
Model evaluation using RMSE and MAE on held-out data.
Key Findings
Market congestion and dasher availability at order time were among the strongest predictors of delivery delay.
Non-linear models (e.g., gradient boosting) significantly outperformed simple linear regression.
Restaurant preparation time variance contributed substantially to overall prediction error.
How to Run
git clone https://github.com/alexjbyoon/DoorDashDeliveryPrediction.git

cd DoorDashDeliveryPrediction

unzip datasets.zip

jupyter notebook code.ipynb
