# Hotel Booking Cancellation Prediction

**Faria Kamal** · Student ID 26172108 · CMP6221 — Computing for AI

A coursework project asking a practical question: **can booking details available at reservation time help a hotel anticipate cancellations?** It compares four classifiers with a majority-class baseline, then uses K-Means to look for groups of similar bookings. Limits are reported as carefully as the scores.

Portfolio case study: <https://Faria-Kamal.github.io/hotel-booking-cancellation.html>  
GitHub: <https://github.com/Faria-Kamal> · LinkedIn: <https://linkedin.com/in/s0ha>

---

## Repository contents

All files are at the repository root:

| File | Purpose |
|---|---|
| `hotel_booking_cancellation_prediction.ipynb` | Main notebook. Code cells begin with a comment naming the section; markdown cells explain each decision. |
| `hotel_bookings.csv` | Source dataset: 119,390 rows × 32 columns. |
| `validation_results.csv` | Validation scores for every model, with and without engineered features. |
| `test_results.csv` | Final held-out test scores for the tuned Random Forest. |
| `cluster_summary.csv` | Mean booking features for each K-Means group. |
| `cluster_cancellation_rates.csv` | Cancellation count and rate for each group. |
| `confusion_matrix.png` | Final test confusion matrix. |
| `correlation.png` | Correlation heatmap used during exploration. |
| `Outlines-2026.ppt` | Presentation outline used for the module submission. |
| `FariaKamal_26172108.pdf` | Project report submitted for the module. |
| `requirements.txt` | Python dependencies for running the notebook locally. |

The notebook looks for `hotel_bookings.csv` in the notebook folder or in `data/`, and asks for an upload when run in Google Colab.

---

## The question

1. **Supervised:** predict whether a booking will be cancelled by comparing Logistic Regression, Decision Tree, Random Forest and KNN against a majority-class baseline.
2. **Unsupervised:** group similar bookings with K-Means and check whether cancellation rates differ across groups.

The model is a planning aid, not an automatic decision-maker.

---

## Data and preparation

- Source: [Hotel Booking Demand (Kaggle)](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand); original paper: Antonio, de Almeida & Nunes (2019), *Data in Brief*, 22, 41–49.
- 119,390 bookings, 32 columns, arrivals from July 2015 to August 2017. `is_canceled` is `1` for a cancelled booking and `0` otherwise.
- Reservation status and its date were excluded because they disclose the final outcome. Fields that could change after booking were also excluded.
- Cleaning removed exact duplicate rows and four rows with missing child counts, labelled missing countries as `Unknown`, and removed negative counts, zero-guest and zero-night bookings. Zero and unusually high daily rates were kept because they may be genuine.
- Cleaned shape: **86,634 rows**.
- Split: 60% development, 20% validation, 20% final test, stratified by the target.

### Feature engineering

Six extra columns were created: `total_nights`, `total_guests`, `has_children`, `weekend_share`, `previous_cancel_rate` and `log_daily_rate`. `log1p` was used so that very high daily rates do not dominate while zero rates remain usable.

Encoding and scaling were learned from training data only. The test set was touched once, for final evaluation.

---

## Notebook structure

The notebook is organised around five classes:

- **DataLoader** — loads the CSV and previews the data.
- **DataAnalyzer** — inspects types, missing values, duplicates and exploratory charts.
- **DataPreprocessor** — cleans rows, engineers features, encodes categories and splits the data.
- **ModelBuilder** — trains and compares models, tunes a Random Forest, evaluates on the held-out test set and saves results.
- **BookingClusters** — fits K-Means, describes the groups and reports cancellation rates per group.

---

## Results

### Validation (selected rows)

| Features | Model | Validation accuracy | F1 |
|---|---|---:|---:|
| Original | Logistic Regression | 75.78% | 0.4264 |
| Original | Decision Tree | 75.84% | 0.3951 |
| Original | Random Forest | 77.72% | 0.4160 |
| Original | KNN | 74.80% | 0.4971 |
| Engineered | Logistic Regression | 76.05% | 0.4335 |
| Engineered | Random Forest | 77.81% | 0.4180 |
| Engineered | KNN | 74.47% | 0.4904 |
| Engineered | Tuned Random Forest | 78.70% | 0.4926 |

### Final held-out test — Tuned Random Forest

| Metric | Value |
|---|---:|
| Training accuracy | 89.05% |
| Test accuracy | **79.53%** |
| Majority-class baseline | **72.32%** |
| Precision (cancelled) | 73.22% |
| Recall (cancelled) | 41.06% |
| F1 (cancelled) | 52.61% |

The model was right about most bookings, but it still raised **720 false alerts** and missed **2,827 actual cancellations**. Accuracy alone would hide that. The gap between training and test accuracy also suggests some overfitting.

### Booking groups (K-Means, k = 4)

| Cluster | Bookings | Mean lead time | Mean daily rate | Mean nights | Cancellation rate |
|---:|---:|---:|---:|---:|---:|
| 0 | 10,279 | 37.32 | 98.48 | 2.70 | 22.77% |
| 1 | 2,503 | 74.36 | 162.30 | 3.51 | 33.52% |
| 2 | 239 | 42.32 | 0.87 | 2.49 | 15.48% |
| 3 | 4,306 | 188.19 | 100.83 | 6.06 | 36.67% |

Group numbers are labels, not rankings. The clusters describe patterns; they do not explain why guests cancel.

---

## Limitations

- Only historical bookings were used, so future performance is unknown.
- Bookings made together may appear in both training and test sets.
- Input details were assumed to be known at prediction time.
- Exact duplicate rows were removed, although some could have been separate real bookings.
- Recall is too low to rely on alone. A next step would be to test cost-sensitive thresholds and validate on newer data.

---

## How to run

### Google Colab

1. Open `hotel_booking_cancellation_prediction.ipynb` in Colab.
2. Run cells from top to bottom.
3. If the CSV is not found, upload `hotel_bookings.csv` when prompted.
4. Result CSVs and PNGs are written to the notebook's working folder.

### Local

```bash
pip install -r requirements.txt
jupyter notebook hotel_booking_cancellation_prediction.ipynb
```

Use Python 3.9 or newer.

---

## Notes for reviewers

- Every cleaning decision is documented in a markdown cell before the code that performs it.
- Code cells begin with a short comment naming the section they belong to.
- All numbers in this README come from the committed results files or the submitted project report; none are estimated for presentation.

No open-source licence has been specified yet. Please ask before reusing the material.
