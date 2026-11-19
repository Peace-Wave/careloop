# CareLoop Clinics — No-show analysis

**Repository link:** `<paste your public GitHub repository URL here>`

## Summary (one finding per Part)
1. **Part 1 (cleaning):** 1,020 raw rows became 990 clean bookings (20 duplicates, 6 unreadable dates and 4 blank statuses removed); 218 of 990 bookings (22.0%) are no-shows.
2. **Part 2 (SQL):** no-shows rise sharply with booking lead time: 16.2% for 1–7 days, 30.0% for 15–30 days and 60.5% for 31+ days; Hadapsar (27.3%) and Malviya Nagar (26.8%) lead among clinics with at least 95 bookings.
3. **Part 3 (model):** logistic regression reaches a test ROC-AUC of 0.789 and, at a 0.35 threshold, catches 57.1% of no-shows (recall) with 49 of 198 test bookings flagged.
4. **Part 4 (GenAI):** `[FILL AFTER RUNNING 04_genai.ipynb: e.g. "Gemini agreed with the expected cause on X of 9 notes; N of the 10 notes came from reminder_sent = No bookings"]`. Checked from the data alone: 5 of the 10 notes belong to appointments with no reminder.

## Recommendation
CareLoop should start by phoning, on the evening before the visit, the bookings the model flags at a 0.35 risk threshold, and should send reminders to every booking. Overall 22.0% of bookings are missed, but long lead times are the clearest warning sign: 60.5% of bookings made 31+ days ahead are no-shows against 16.2% for 1–7 days, so far-ahead bookings deserve an extra confirmation call. In the fitted model, holding other features constant, a reminder goes with about 69% lower odds of a no-show (odds ratio 0.31), and reminders are a cheap lever. Flagging 49 of 198 test bookings caught 57.1% of the real no-shows, which turns about one empty slot in five into a phone call. Gomti Nagar (33.3%) and Hadapsar (27.3%) are the clinics to pilot in first; Gomti Nagar has only 90 bookings, so treat its rate with care.

## Model chosen and why
Logistic regression (with a scaler inside a pipeline). It had the highest 5-fold cross-validated ROC-AUC on the training set (0.774, against 0.678 for the depth-3 tree, 0.747 for the random forest and 0.767 for the tuned forest), chosen without touching the test set. The threshold of 0.35 beat 0.5 on out-of-fold training F1 (0.489 vs 0.347) with much higher recall (0.467 vs 0.249). One test evaluation gave precision 0.571, recall 0.571, 49 flagged and ROC-AUC 0.789. Scores are in `test_risk_scores.csv`.

## Limitations
1. The 10 notes are an illustration and cannot show the real reasons clinic-wide.
2. Medians, quartiles and outlier fences were calculated on all 990 rows before the split, so test rows had a small indirect influence on the cleaning.
3. The same patient can appear in both train and test, and the split is random rather than past-to-future. The data are also observational, so associations such as the reminder effect are not causes.

## Files
`01_ingest_clean.ipynb` (Tasks 1.1–1.6, plus the Python check for Task 2.2) · `02_queries.sql` · `03_model.ipynb` · `04_genai.ipynb` · `rejected_rows.json` · `appointments_clean.csv` · `clinics_enriched.csv` · `load_data.sql` · `test_risk_scores.csv` · `notes_analysed.csv` · `data/` (three original files).
