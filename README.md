# Salary Bucket Predictor & Skill Recommender

An interactive, client-facing dashboard that lets you answer the 2026 Stack Overflow Developer Survey questions yourself and see what an ordinal logistic regression model predicts for your salary bucket — then ranks the skills/tools most likely to move you up it.

It answers two questions, applied to you specifically:

1. Given your own survey answers, what's the full probability distribution over the 15 ordered salary buckets — not just a single guess?
2. Of the tools/technologies you don't already use, which three would the model predict the largest salary-bucket gain from learning?

Live dashboard: [https://belinda-serafine.github.io/mie1624-salary-buckets-prediction/](https://belinda-serafine.github.io/mie1624-salary-buckets-prediction/)

## Key findings (about the model)

| Metric | Result |
|---|---|
| Features | 658 selected (union of above-mean mutual information and above-mean random-forest importance, out of 1,132 one-hot/engineered features) |
| Model | Cumulative-threshold ordinal logistic regression — 14 binary `LogisticRegression` models, implemented from scratch |
| Best hyperparameters | C = 0.1, L1 penalty (grid search over C × {L1, L2}; mean CV MAE 1.492 buckets) |
| Test performance | Accuracy 0.299, MAE 1.424 buckets (train: 0.394 / 1.301) |
| Cross-validation (5-fold) | Accuracy 0.270 ± 0.011, MAE 1.567 ± 0.032 buckets |

## What's in the dashboard

- A survey form, grouped into 5 collapsible sections (Demographics & Role, Experience & Knowledge, Tech Stack, AI Usage & Workflow, Community & Context), asking only the **55 original survey questions** that back the model's 658 selected features — never encoded or engineered values directly
- On submit: your answers are converted into the model's one-hot feature layout, the **training-fitted** scaler is applied (no refitting), and the ordinal logistic regression runs entirely client-side
- A bar chart of your predicted probability across all 15 ordered salary buckets, with the most likely bucket highlighted
- Your top 3 recommended skills/tools to learn, ranked by predicted gain in *expected* salary bucket (∑ k·P(bucket=k)), with a note on why that's an association, not a causal claim
- A methods summary, data-privacy note, and a plain-language glossary of the statistical/ML terms used

## Methods

- Target: `ConvertedCompYearlyBucket`, 15 ordered annual-compensation ranges
- Feature selection: mutual information + random-forest importance, union of above-mean features from each
- Engineered features: `TechStackCount`, `PreCareerCoding`, `SeniorManager` — computed automatically from your raw answers, never asked for directly
- Scaling: `StandardScaler` fit on training data only, applied (not refit) to new predictions
- Model: ordinal logistic regression via K−1 cumulative binary models; probabilities obtained by taking a running minimum across thresholds, then differencing
- Tuning: grid search over C and regularization type (L1/L2), selected by mean cross-validated MAE

## Data privacy

This page ships only the **trained model's aggregated parameters** — which one-hot options survived feature selection, their coefficients, and the training-fitted scaler's mean/scale — not any individual survey response. Everything you type into the form stays in your browser; the prediction runs client-side in JavaScript and nothing is sent to a server.

## Running locally

The dashboard is a single self-contained `index.html` file. You can open it in any modern browser. An internet connection is needed to load Chart.js.

## Credits

Analysis and dashboard by Belinda Serafine, MEng (Mechanical & Industrial Engineering), University of Toronto. Prepared for MIE1624: Introduction to Data Science, Analytics and AI (Fall 2026).
The dashboard was built with the help of Claude (Anthropic).
Survey data: Stack Overflow Developer Survey.
