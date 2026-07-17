# What Drives the Price of a Used Car? (Minus Rams from the Data Set)

A data analysis and pricing tool built for a used car dealership, using a dataset of 426,880 used vehicle listings to identify what factors most influence sale price and to provide a fair-market price estimate for incoming inventory.

**NOTE:** This data set deleted all vehicles with the manufacturer Ram as there were significant issues with predictions from this manufacturer.

**📓 Full analysis notebook:** [used_car_prices_minus_RAM.ipynb](used_car_prices_minus_RAMt.ipynb)

---

## Business Problem

A used car dealership needs to price incoming vehicles competitively and fairly. This project identifies which vehicle attributes (age, mileage, condition, drivetrain, manufacturer, and more) most strongly predict sale price, and builds a model the dealership can use to estimate a fair market price for a car before it goes on the lot.

## Summary of Findings

**Our best model predicts price within about $6,988 on average** (RMSE), cutting prediction error by roughly 45% compared to simply pricing every car at the inventory average (~$12,776 error). We tested the model on cars it had never seen before, and it performed almost identically to how it performed on cars it trained on (train RMSE $6,950 vs. test RMSE $6,988) — meaning it should generalize well to new inventory coming through the lot.

This version of the analysis **removes all Ram vehicles** from the dataset after an earlier pass found that Ram listings showed a systematic data quality issue (see "Where the model struggles" below).

### What actually drives price

Holding all else equal, brand and vehicle type both matter, but brand effects are considerably larger:

- **Rare luxury and exotic brands command large premiums.** A Ferrari is predicted to sell for about $95,300 more than the reference manufacturer; Aston-Martin about $27,400 more; Porsche about $9,900 more.
- **Vehicle type still matters, but less dramatically.** Trucks and pickups are priced roughly $5,800–$7,200 higher than SUVs; off-road vehicles about $4,900 higher.
- **A clean title is worth real money.** Cars with a clean title sell for about $4,200 more than cars with any other title status (salvage, rebuilt, lien, or missing).
- **Several mainstream brands sell at a discount** relative to the reference manufacturer — Kia, Mitsubishi, Hyundai, Nissan, Subaru, and Volkswagen all showed negative coefficients in the $4,800–$7,800 range, consistent with their positioning as budget-to-midrange brands.
- **Note:** the largest coefficients (Ferrari, Aston-Martin, Morgan, Harley-Davidson) come from manufacturers with very few listings in the dataset, so these dollar figures should be read as directionally correct but imprecise.

### Where the model struggles

- **Removing Ram did not fully resolve the data quality issue — it moved to other truck-heavy manufacturers.** Ford, GMC, and Chevrolet trucks/pickups now account for roughly two-thirds of our worst 50 predictions, showing the same pattern originally found in Ram listings: an actual price near $0–$1,100 against a model prediction of $50,000+. This points to data entry errors, placeholder prices, or damaged/parts-only vehicles that weren't fully caught by our cleaning steps — a broader issue than any single manufacturer.
- **Rare luxury vehicles are still underpriced by the model.** Ferrari, Aston-Martin, Porsche, and Land Rover are all underrepresented in our dataset, so the model hasn't seen enough examples to learn their true price premium and defaults toward more typical prices for similar body styles.

## Recommendations

1. **Spot-check truck/pickup listings across all manufacturers for data errors**, not just Ram — the Ford, GMC, and Chevrolet pattern shows this issue is broader than one brand.
2. **Rely on manual appraisal for exotic/luxury vehicles** (Ferrari, Aston-Martin, Porsche, Land Rover), which are too rare in this dataset for the model to price reliably.
3. **Use the model as a screening tool, not a final price-setter.** With a typical error of roughly $7,000, it's best used to flag cars priced well outside the expected range for their year, mileage, and type — final pricing should combine the model's estimate with human judgment.
4. **Consider a more targeted data-quality fix going forward.** Removing Ram improved accuracy, but our analysis suggests the real problem is bad *listings*, not bad *manufacturers*. Filtering suspiciously low truck prices directly — regardless of manufacturer — may preserve more usable data than removing brands wholesale.
5. **Expand training data over time**, especially for underrepresented vehicle types and manufacturers, to improve future accuracy.
6. **Consider adding features** not in this dataset — accident history, number of owners, regional demand — which likely explain some of the remaining prediction error.

## How to Use the Price Estimator

The notebook includes an interactive tool for getting a quick price estimate. Open the notebook, run all cells, then run:

```python
price_estimator_tool()
```

You'll be prompted for the car's year, manufacturer, type, mileage, and drivetrain, and the tool will return an estimated fair market price — along with a warning if you enter a manufacturer or type the model hasn't seen before.

## Methodology (Technical Summary)

- **Data:** 426,880 used vehicle listings (Kaggle), cleaned to remove missing critical fields and price/year/mileage outliers (IQR method, per manufacturer), with all Ram vehicles removed
- **Models tested:** Linear Regression, Ridge Regression (cross-validated via GridSearchCV), and Polynomial Regression (degrees 2 and 3) with Linear and Ridge
- **Best model:** Degree-3 Polynomial + Linear Regression, Test RMSE $6,987.80, Test MAE $4,769.98
- **Evaluation metric:** RMSE, chosen because it's dollar-interpretable and appropriately penalizes large mispricing errors, which carry more business risk than several small ones
- **Feature importance:** confirmed via permutation importance, since the best model's polynomial/scaled coefficients aren't directly interpretable — `year` and `odometer` are the two strongest overall drivers of predicted price

---

*See the full notebook for detailed exploratory analysis, visualizations, model comparisons, and coefficient interpretations.*
