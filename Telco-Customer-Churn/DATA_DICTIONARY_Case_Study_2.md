# Case Study 2 data dictionary

Source: `Datasets/telco_churn.csv` in the supplied project. The inspected version has 5,043 rows and 22 raw columns. The model target is `Churn`. The case study PDF describes 21 business fields; the extra field `Unnamed: 0` is an export index, not a business field.

| CSV field | Meaning and unit | Raw-file details and treatment |
|---|---|---|
| `Unnamed: 0` | Exported row number. | Not a business attribute; values repeat. Dropped. |
| `customerID` | Unique customer reference. | Unique in inspected file; not a predictive feature. Kept only for traceability. |
| `gender` | Recorded gender, Female or Male. | Categorical; one-hot encoded. Fairness/governance review needed before real decisions. |
| `SeniorCitizen` | Senior citizen indicator. | Mixed True/False and 1/0 spellings; normalized to Yes/No. |
| `Partner` | Partner indicator. | Mixed True/False and Yes/No spellings; normalized. |
| `Dependents` | Dependents indicator. | Mixed True/False and Yes/No spellings; normalized. |
| `tenure` | Months as a customer. | Integer month count, 0–72 in source; zero-tenure rows removed because the file has no valid accumulated charge history for them. |
| `PhoneService` | Phone-service indicator. | Mixed boolean/text spellings; normalized to Yes/No. |
| `MultipleLines` | Whether customer has multiple phone lines. | 269 blanks in inspected file, all paired with PhoneService=False; filled as `No phone service`. Any unexplained blank -> `Unknown`. |
| `InternetService` | Internet category: DSL, Fiber optic, or No. | `No` means no internet service; retained as its own category. |
| `OnlineSecurity` | Online security add-on. | 651 blanks; the same 651 rows have InternetService=No. Those blanks -> `No internet service`; other unexplained blank -> `Unknown`. |
| `OnlineBackup` | Online backup add-on. | Same 651-row no-internet blank pattern and treatment. |
| `DeviceProtection` | Device protection add-on. | Same 651-row no-internet blank pattern and treatment. |
| `TechSupport` | Tech-support add-on. | Same 651-row no-internet blank pattern and treatment. |
| `StreamingTV` | Streaming TV add-on. | Same 651-row no-internet blank pattern and treatment. |
| `StreamingMovies` | Streaming movies add-on. | Same 651-row no-internet blank pattern and treatment. |
| `Contract` | Month-to-month, One year, or Two year contract. | Categorical; one-hot encoded. |
| `PaperlessBilling` | Paperless-billing indicator. | Mixed True/False and Yes/No spellings; normalized. |
| `PaymentMethod` | Electronic check, mailed check, automatic bank transfer, or automatic credit card. | Categorical; one-hot encoded. |
| `MonthlyCharges` | Monthly recurring bill amount; source does not state currency. | Decimal numeric; median-imputed from training data if missing, standardized. |
| `TotalCharges` | Accumulated charges to date; source does not state currency. | Text in CSV; parsed to number. Blank for zero-tenure cases; remaining missing values are imputed using training data only. |
| `Churn` | Customer left service: Yes or No. | Supervised target. Raw values mix True/False and Yes/No, with one missing label. Normalized to Yes/No; missing-target row excluded from supervised training. |

## Terms that should not be collapsed

- `No`: the feature applies and the answer is no.
- `No phone service`: there is no underlying phone subscription, so multiple-line status is not applicable.
- `No internet service`: there is no underlying internet subscription, so internet add-ons are not applicable.
- `Unknown`: the CSV gives no defensible reason for a blank.

## Source quality snapshot

- Dimensions: 5,043 rows × 22 columns.
- `customerID`: 5,043 distinct values; no duplicate IDs observed.
- Whole-row duplicates: none observed.
- Raw `Churn`: `False` 2,219; `No` 1,487; `True` 780; `Yes` 556; missing 1.
- `MultipleLines`: 269 missing; each aligns with a no-phone row.
- Each of six internet add-on columns: 651 missing; each aligns with `InternetService=No`.
- `TotalCharges`: text values and blanks; eight zero-tenure rows in this version.

These counts describe the supplied copy only. If the Courseware CSV or another version is used, rerun the audit cells and revise the counts. The `AvgMonthlySpendToDate` feature in the notebook is computed as `TotalCharges / tenure`; it is an approximate historical monthly average and can contain missing values if either component is missing. Numeric imputation occurs inside training folds, not on the full dataset.
