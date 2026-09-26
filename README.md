# AI-Powered Job Posting Fraud Detector

An end-to-end project combining a trained machine learning classifier with an interactive Power BI dashboard to detect and explain potentially fraudulent job postings.

## The Problem

Online job boards lose user trust when scam postings slip through — fake "earn money from home" listings scam job seekers, and manually reviewing every posting doesn't scale. This project automates the first pass of that review: flagging the most likely fraudulent postings *before* a human reviewer looks at them, along with the specific reasons each one was flagged.

## Dataset

[Real or Fake: Fake Job Description Prediction](https://www.kaggle.com/datasets/shivamb/real-or-fake-fake-jobposting-prediction) — 17,880 real job postings (2012–2014), ~866 (4.84%) labeled fraudulent.

## Approach

1. **Text cleaning** — combined title, company profile, description, and requirements into one field per posting; lowercased, stripped HTML and punctuation.
2. **Feature extraction** — TF-IDF vectorization (5,000 features, English stop words removed).
3. **Model** — Logistic Regression with `class_weight='balanced'` to handle the ~5% fraud rate, chosen specifically for interpretability over black-box alternatives.
4. **Explainability** — extracted per-posting "red flag" keywords directly from the model's learned coefficients, rather than treating it as a black box.
5. **Dashboard** — exported scored results into Power BI: a summary view with interactive filtering, and a drill-through page for industry-level detail.

## Results

| Metric | Value |
|---|---|
| Accuracy | 97% |
| Recall (fraud class) | 90% |
| Precision (fraud class) | 63% |
| Fraud caught / total fraud in test set | 155 / 173 |

Accuracy alone is misleading on this imbalanced dataset — a model that always predicted "real" would still score ~95%. Precision/recall and a stratified train/test split were used to evaluate honestly.

**A known limitation, found and validated during evaluation:** the model picked up on industry-specific vocabulary (e.g. oil & gas, healthcare terms) that reflects scam clusters specific to this dataset's time period — not universal fraud language. A real ER nurse job posting was incorrectly flagged as an example of this. This is why the system is designed to flag postings for human review, not auto-reject them.

## Power BI Dashboard

- **Summary page** — fraud rate and flagged-posting totals, fraud rate by industry, interactive slicers, a sortable table of flagged postings with individual legitimacy scores and flag reasons.
- **Industry detail page (drill-through)** — right-click any posting's industry to drill into a filtered view: industry-specific fraud rate, average legitimacy score, and a company-logo-presence breakdown.
<img width="1243" height="686" alt="image" src="https://github.com/user-attachments/assets/ed70cdfc-e1b1-4811-9fad-d6970ebdfd8f" />
<img width="1233" height="693" alt="image" src="https://github.com/user-attachments/assets/36bca97f-5e12-45dd-95cd-0e6100ec892e" />


## Tech Stack

Python (pandas, scikit-learn), TF-IDF, Logistic Regression, Power BI, DAX

## How to Run
1. Download the dataset from Kaggle and place `fake_job_postings.csv` in the same directory.
2. Open `AI-Powered Job Posting Fraud Detector.ipynb` in Jupyter or Google Colab.
3. Run all cells — this cleans the data, trains the model, evaluates it, and exports `scored_postings.csv`.
4. Open the Power BI file (or import `scored_postings.csv` fresh) to explore the dashboard.

## Future Improvements
- Package the cleaning + vectorizing + predicting steps into a reusable function with the trained model/vectorizer saved via `joblib`, so new postings can be scored without rerunning the full notebook.
- Expand features beyond text (e.g. posting recency, poster history) to reduce reliance on industry-specific vocabulary.
