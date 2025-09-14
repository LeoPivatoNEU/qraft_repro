# QRAFT Reproduction

This repository provides a minimalist reproduction of the noise-aware prediction model from the QRAFT study.
A small decision-tree ensemble is trained on simulated circuit data (``qraft_data.csv``) to estimate noise-mitigated
probabilities, and its performance is compared against the raw noisy backend.

## Dataset and Model
- **Dataset**: ``qraft_data.csv`` (~1.3k rows) generated from the ``FakeVigoV2`` noise model. It includes basic circuit
  descriptors (width, depth, gate counts, Hamming weight) and percentile summaries of forward and reverse runs.
- **Model**: a bagged decision-tree ensemble defined in ``qraft_repro.ipynb``.

## Results
The notebook evaluates the model on a held-out test set. Lower values indicate better agreement with ideal
probabilities.

| Source        | MAE   | RMSE  | TVD    |
|---------------|-------|-------|--------|
| Noisy backend | 0.0104| 0.0190| 6.7724 |
| Model output  | 0.0724| 0.1401| 7.0951 |

The model-corrected probabilities deviate more from the ideal than the raw noisy measurements, showing that this
simple ensemble fails to mitigate hardware noise.

## Why it underperforms the QRAFT paper
The original QRAFT publication (https://dl.acm.org/doi/pdf/10.1145/3445814.3446743) reported substantial error
reductions using tuned ensemble models and extensive datasets. This reproduction diverges due to:

1. **Limited data and features** – a single small CSV lacks per-qubit calibration metrics, qubit mapping, and raw shot
   statistics that the paper exploited.
2. **Model capacity and tuning** – the basic tree ensemble is lightly tuned and overfits, as signaled by the large test
   MAE.
3. **Hardware noise regime** – the ``FakeVigoV2`` simulator reflects relatively modern, lower-noise devices, leaving
   little room for classical correction.
4. **Evaluation mismatch** – the notebook normalizes predictions over a tiny test set, while QRAFT evaluated broader
   circuit and backend contexts.

## Running the notebook
Open ``qraft_repro.ipynb`` in Jupyter and execute all cells. The notebook loads ``qraft_data.csv``, trains the model, and
prints the metrics above.

## Future work
- Expand the dataset with real hardware runs and qubit-level calibration data.
- Incorporate richer feature engineering and stronger models (e.g., gradient-boosted trees).
- Align evaluation with the original QRAFT setup to enable direct comparisons.

