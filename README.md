`housing_price_corrected.ipynb` contains **code cells only** (no inline commentary). This
file documents every fix/addition made relative to the original uploaded notebook.

## Bug fix
- The original final results table used `rmse_lr` for every row instead of each model's
  own RMSE. All models now get their correct, distinct value.

## Additions to fully satisfy the assignment brief

**1. EDA visualizations** (previously only printed numbers, no charts):
- `SalePrice` distribution (histogram + KDE)
- `SalePrice` vs `GrLivArea` scatter plot
- `SalePrice` by neighborhood (boxplot, top 8 neighborhoods)
- Correlation heatmap of the top 10 features most correlated with `SalePrice`

**2. Outlier handling broadened**: IQR-based capping now applied to 13 continuous
numeric columns prone to skew/extreme values, not just `SalePrice` and `GrLivArea`:
`SalePrice, GrLivArea, LotArea, LotFrontage, TotalBsmtSF, 1stFlrSF, 2ndFlrSF,
GarageArea, MasVnrArea, WoodDeckSF, OpenPorchSF, EnclosedPorch, MiscVal`.
Values are capped (winsorized), not dropped, to preserve sample size.

**3. GridSearchCV hyperparameter tuning added for every model**, not just XGBoost:
- Decision Tree: `max_depth`, `min_samples_split`, `min_samples_leaf`
- Random Forest: `n_estimators`, `max_depth`, `min_samples_split`
- XGBoost: `n_estimators`, `max_depth` (unchanged from original)

## Other fixes carried over from v1
- `Id` column dropped before training (it's a row identifier, not a feature).
- Feature engineering added: `TotalSF`, `HouseAge`, `TotalBath`.
- Redundant `.mode()` fillna removed (dead code — no NaNs remain after median fillna).
- 5-fold cross-validation reported for every tuned model, to confirm the grid search
  results generalize and aren't just fitting one split.

## Validated results (executed against the real Kaggle train.csv, 1460×81)

| Model | Test RMSE | Test R² | 5-fold CV RMSE |
|---|---:|---:|---:|
| XGBoost (tuned) | $20,526 | 0.914 | $20,817 ± $2,220 |
| Random Forest (tuned) | $20,718 | 0.912 | $21,927 ± $3,459 |
| Linear Regression | $22,402 | 0.897 | $21,704 ± $3,666 |
| Decision Tree (tuned) | $27,888 | 0.841 | $30,287 ± $3,080 |

XGBoost (tuned) and Random Forest (tuned) are close to tied — the gap is within one
CV standard deviation of each other — with Linear Regression a surprisingly close
third given the dataset's many non-linear feature interactions, and the single
Decision Tree trailing as expected.

## Running it
Requires `train.csv` (Kaggle "House Prices - Advanced Regression Techniques") in the
same folder.
```bash
pip install pandas numpy matplotlib seaborn scikit-learn xgboost
jupyter nbconvert --to notebook --execute --inplace housing_price_corrected.ipynb
```
