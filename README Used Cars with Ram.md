# What Drives the Price of a Used Car?

A data analysis and pricing tool built for a used car dealership, using a dataset of 426,880 used vehicle listings to identify what factors most influence sale price and to provide a fair-market price estimate for incoming inventory.

** 📓 Full analysis notebook: [View Notebook](used_car_price_predictor_with_RAMt.ipynb) **
---

## Business Problem

A used car dealership needs to price incoming vehicles competitively and fairly. This project identifies which vehicle attributes (age, mileage, condition, drivetrain, manufacturer, and more) most strongly predict sale price, and builds a model the dealership can use to estimate a fair market price for a car before it goes on the lot.

## Summary of Findings

**Our best model predicts price within about $7,100 on average** (RMSE), cutting prediction error nearly in half compared to simply pricing every car at the inventory average (~$13,200 error). We tested the model on cars it had never seen before, and it performed just as well as on cars it trained on — meaning it should generalize well to new inventory coming through the lot.

### What actually drives price

Holding all else equal:
- **Vehicle type matters a lot.** Pickup trucks are priced roughly $6,600 higher than SUVs; off-road vehicles about $5,400 higher.
- **A clean title is worth real money.** Cars with a clean title sell for about $4,400 more than cars with any other title status (salvage, rebuilt, lien, or missing).
- **Drivetrain and fuel type pull price down for some categories.** Front-wheel-drive vehicles and gas-powered vehicles both showed lower predicted prices relative to their category's reference point — likely reflecting that these are common in more budget-oriented vehicle segments (economy sedans) rather than premium ones (4WD trucks, diesel/luxury vehicles).

### Where the model struggles

- **Ram vehicles are a data quality flag.** Ram accounts for 38% of our worst 50 predictions — the model consistently predicts several thousand dollars, but the actual listed price is near $0. This points to likely data entry errors or incomplete listings rather than a modeling flaw, and should be manually reviewed before trusting predictions on Ram inventory.
- **Rare luxury vehicles are underpriced by the model.** Ferrari, Aston-Martin, and similarly rare brands are underrepresented in our dataset, so the model hasn't seen enough examples to learn their true price premium. These should be manually appraised rather than priced using this tool.

## Recommendations

1. **Use this model as a screening tool, not a final price-setter.** With a typical error of ~$7,000, it's best used to flag cars priced well outside the expected range for their year, mileage, and type — final pricing should combine the model's estimate with human judgment.
2. **Manually review Ram listings** for data errors before trusting model output on that inventory.
3. **Rely on manual appraisal for exotic/luxury vehicles**, which are too rare in this dataset for the model to price reliably.
4. **Expand training data over time**, especially for underrepresented vehicle types, to improve future accuracy.
5. **Consider adding features** not in this dataset — accident history, number of owners, regional demand — which likely explain some of the remaining prediction error.

## How to Use the Price Estimator

The notebook includes an interactive tool for getting a quick price estimate. Open the notebook, run all cells, then run:

```python
price_estimator_tool()
```

You'll be prompted for the car's year, manufacturer, type, mileage, and drivetrain, and the tool will return an estimated fair market price — along with a warning if you enter a manufacturer or type the model hasn't seen before.

## Methodology (Technical Summary)

- **Data:** 426,880 used vehicle listings (Kaggle), cleaned to ~345K rows after removing missing critical fields, price/year/mileage outliers (IQR method, per manufacturer)
- **Models tested:** Linear Regression, Ridge Regression (cross-validated via GridSearchCV), and Polynomial Regression (degrees 2 and 3) with Linear and Ridge
- **Best model:** Degree-3 Polynomial + Linear Regression, Test RMSE $7,129.93, Test MAE $4,835.30
- **Evaluation metric:** RMSE, chosen because it's dollar-interpretable and appropriately penalizes large mispricing errors, which carry more business risk than several small ones

---

*See the full notebook for detailed exploratory analysis, visualizations, model comparisons, and coefficient interpretations.*
