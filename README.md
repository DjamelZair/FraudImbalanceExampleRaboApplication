# Fraud detection under extreme class imbalance

**Detecting card fraud when only 1 payment in 578 is fraudulent — and deciding what to do about it.**

[![The visual summary](figures/poster-preview.png)](https://djamelzair.github.io/FraudImbalanceExampleRaboAplication/)

📊 **[Read the visual summary](https://djamelzair.github.io/FraudImbalanceExampleRaboAplication/)** — a one-page walkthrough written for non-specialists, no statistics required.

📓 **[Read the full notebook](https://djamelzair.github.io/FraudImbalanceExampleRaboAplication/notebook.html)** — every chart interactive, all reasoning in order.

---

## What this is

Most fraud-detection notebooks stop at a model and a score. The hard part of this problem is everything that comes after: how much confidence the numbers deserve, what an error actually costs, and what a bank should *do* when the model is unsure.

This notebook is about that second half.

284,807 real card transactions over two days. 492 are fraudulent — 0.173%. A model that labels every transaction as legitimate scores **99.83% accuracy** and catches nothing. Every evaluation decision here follows from that single fact.

## Result on the untouched test period

The final model and policy were locked before the test period was scored, and evaluated exactly once.

| | |
|---|---|
| Fraud caught | **64 of 74** (86%) |
| Legitimate customers held or declined | **114 of 56,672** (0.20%) |
| Precision of the strictest response | **91%** — 50 of 55 declines were genuine fraud |
| Average precision | **0.78**, 95% interval **[0.68, 0.86]** |

The interval is the honest headline. With 74 fraud cases in the test period, a single number quoted to three decimal places would be false precision.

## What this notebook does that most do not

**Reports uncertainty on every headline number.** Metrics carry bootstrap intervals. Model comparison uses a *paired* bootstrap, which shows the calibrated model beating the transparent one in 100% of resamples even though their individual intervals overlap heavily.

**Treats the two errors as economically different.** Missed fraud is costed at the value of that specific transaction. A false alert is costed per alert — and because nobody has said what that costs, it is swept across a plausible range rather than guessed once. The cost-optimal operating point moves more than twentyfold across that range, which is why this notebook does not ship a single "optimal" threshold.

**Shows that the optimum is partly noise.** Holding the cost assumption fixed and resampling the same data still moves the cost-optimal alert volume by a factor of two.

**Replaces the binary decision with a graded one.** Rather than approve-or-decline, the calibrated probability drives four responses — approve, automated verification, analyst review, decline. Matched on fraud caught, this cuts the cost of customer disruption by 98% against a single blocking threshold, because a wrong verification costs a customer seconds and a wrong decline costs them a refused payment at a till.

**Asks who bears the false alarms.** No protected attributes exist in this data, and the notebook says so rather than faking an audit. What it can test, it does: the smallest payments are stopped at **2.8× the average rate**. It then implements a cap on that disparity and prices it — one fraud case over the validation period.

**Prices interpretability in fraud cases, not metrics.** At an equal budget of 50 alerts, the transparent logistic model catches 10 fewer frauds than the opaque one.

**Predicts its own degradation, then checks it.** Precision was expected to shift between periods purely because the fraud rate changed. The prediction, written down before the test scores existed, was 2.6%. The observed value was 2.4%.

**Keeps a promise made early on.** Removing 1,081 duplicate rows was flagged at the time as an assumption to be tested, not a cleaning step. It is tested at the end by re-running the whole pipeline with every row retained; the difference is smaller than the sampling noise.

## Method in brief

- **Chronological splits** (60/20/20 by time), never random — a fraud model scores transactions that happen *after* the ones it learned from.
- **Stability-aware feature selection**: features are kept on whether they contribute consistently across chronological folds, not on the importance a single fitted model assigns them.
- **Leakage-safe pipelines** throughout; every scaler and imputer is fitted inside the fold.
- **Chronological hyperparameter search** with the positive-class weight treated as a tunable decision rather than fixed to the imbalance ratio.
- **Calibration** on an internal slice carved from the training period, selected under a rule fixed in advance: among mappings that preserve the ranking, take the best calibrated.
- **Average precision** as the primary metric; ROC-AUC reported but never decisive. The notebook shows a case where ROC-AUC alone would have led to the wrong choice.

## Running it

```bash
git clone https://github.com/DjamelZair/FraudImbalanceExampleRaboAplication.git
cd FraudImbalanceExampleRaboAplication
pip install -r requirements.txt
```

The dataset is 150 MB and is not stored in this repository. Download `creditcard.csv` into the repository root — see [`data/README.md`](data/README.md) for sources. Then:

```bash
jupyter lab fraud_detection.ipynb
```

It runs top to bottom in roughly 40 minutes on a laptop. Outputs are committed, so it can also just be read.

## Data

284,807 anonymised card transactions from September 2013, collected by Worldline and the Machine Learning Group at Université Libre de Bruxelles. Features `V1`–`V28` are principal components published in place of the original variables for confidentiality; `Time` and `Amount` are unmodified.

> Dal Pozzolo, A., Caelen, O., Johnson, R.A., Bontempi, G. (2015). *Calibrating Probability with Undersampling for Unbalanced Classification.* IEEE Symposium on Computational Intelligence and Data Mining.

## Note

Built as a worked example for a Rabobank application. The colour palette was chosen to suit that context; this repository is independent work and is not affiliated with or endorsed by Rabobank.

---

Djamel Zair · [github.com/DjamelZair](https://github.com/DjamelZair)
