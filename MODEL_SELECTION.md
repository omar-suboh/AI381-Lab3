# Model Selection

## The Question

The model predicts whether a customer will churn. A false negative means missing a customer who may need retention support, while a false positive may waste retention resources.

## Candidates

| Candidate | Why I tried it | AUC | F1 | F1 per slice (lowest) | p95 latency (ms) | Size (KB) | MLflow run ID |
|---|---|---|---|---|---|---|---|
| Logistic Regression | Simple and fast baseline | 0.798 | 0.495 | 0.25 (one-year / two-year) | 5.0 | 4 | 90663792bd394aea8fa393c4c7834584 |
| Random Forest | Captures nonlinear patterns | 0.759 | 0.454 | 0.22 (one-year) | 15.0 | 3160 | a22259c25d174d2c8d175f34e9fc12a2 |
| Gradient Boosting | Sequentially improves weak learners | 0.748 | 0.417 | 0.22 (two-year) | 5.4 | 142 | 006ab2a6be984a379db0450e10dc1e4b |

## The Champion

I selected Logistic Regression as the champion because it achieved the highest overall F1 score (0.495) and AUC (0.798) among the three candidates.

It also had the lowest measured p95 latency (5.0 ms) and the smallest model size (4 KB).

All three models met the 25 ms latency budget in the local measurements.

Logistic Regression was promoted successfully and registered in MLflow as `churn-model`, version 1, with the `champion` alias.

The trade-off is that Logistic Regression is a simpler linear model and may not capture complex nonlinear relationships as well as tree-based models. However, it performed better on this dataset.

The contract-type comparison showed that Logistic Regression had the highest weakest-slice F1 (0.25), compared with 0.22 for both Random Forest and Gradient Boosting. However, the relatively low scores indicate that predictions for some contract groups still need improvement.

## My Promotion Rules

- **Latency budget (p95):** 25 ms.
- **Minimum F1 gain:** 0.005.

I chose a 25 ms latency budget to keep predictions fast. The minimum F1 gain of 0.005 helps avoid replacing the champion for very small improvements.

If there is no existing champion, the best eligible candidate can be promoted.

If a champion already exists, a new model must satisfy the latency budget and the required F1 improvement before promotion.

## What I Would Check Next

I would review F1 scores for each contract type to identify groups where the model performs poorly.

I would also monitor data drift because customer behavior can change over time.

Finally, I would test latency under different workloads to ensure the model remains reliable in production.