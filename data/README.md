# Data

The notebook expects `creditcard.csv` in the **repository root**. It is roughly
150 MB, which is why it is not committed here.

## Where to get it

- **Kaggle** — [Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
- **OpenML** — dataset ID [1597](https://www.openml.org/d/1597)

From Python, without a Kaggle account:

```python
from sklearn.datasets import fetch_openml
import pandas as pd

bundle = fetch_openml(data_id=1597, as_frame=True)
frame = bundle.frame
frame.to_csv("creditcard.csv", index=False)
```

The notebook's first data-quality cell strips quotation marks from the `Class`
column and casts it to an integer, so either the Kaggle export or the OpenML
export will load correctly.

## What is in it

| Column | Meaning |
|---|---|
| `Time` | Seconds elapsed since the first transaction in the dataset |
| `V1`–`V28` | Principal components published in place of the original features |
| `Amount` | Transaction value |
| `Class` | 1 for fraud, 0 for legitimate |

284,807 rows, 492 of them fraudulent (0.173%). Collected over two days in
September 2013 by Worldline and the Machine Learning Group at Université Libre
de Bruxelles.

`Time` is measured from the start of the collection window rather than as clock
time. The notebook's distributional analysis explains why this rules out a
time-of-day fairness check on a chronological split.

## Citation

Dal Pozzolo, A., Caelen, O., Johnson, R.A., Bontempi, G. (2015).
*Calibrating Probability with Undersampling for Unbalanced Classification.*
IEEE Symposium on Computational Intelligence and Data Mining (CIDM).
