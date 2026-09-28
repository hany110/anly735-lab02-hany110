# ANLY 735 Replication Laboratory #2

## Can the Model Keep Learning?

This repository contains a proxy replication for ANLY 735 — Research Seminar in Predictive AI.

The experiment investigates a stability–plasticity question motivated by Klein et al. (2024), *Plasticity Loss in Deep Reinforcement Learning: A Survey*:

> After learning one condition, does a neural network remain as effective at learning when the input conditions change?

Because the anchor paper is a survey rather than a single primary experiment, this project does not attempt to reproduce one original Klein et al. numerical result. Instead, it uses a controlled supervised-learning experiment to test a narrower phenomenon discussed in the survey: whether a previously trained model adapts more slowly than an equivalent freshly initialized model after a nonstationary change.

## Repository Structure

```text
.
├── ANLY_735_replication_lab_02_completed_with_readme.qmd
├── references.bib
├── README.md
├── code/
│   └── ANLY_735_replication_lab02_hany_raza.ipynb
├── data/
│   ├── lab2_all_learning_curves.csv
│   ├── lab2_summary.csv
│   ├── lab2_prechange_accuracy.csv
│   ├── lab2_thresholds.csv
│   └── lab2_stability_plasticity_diagnostic.csv
└── figures/
    ├── condition_B_learning_trajectory.png
    └── condition_A_retention.png
```

The raw scikit-learn Digits dataset does not need to be stored in the repository because it is loaded directly through `sklearn.datasets.load_digits()`.

## Experiment Summary

The experiment uses the scikit-learn Digits dataset with 1,797 observations and 64 pixel features.

Two input conditions are defined:

- **Condition A:** the same digit images after one fixed random permutation of the 64 pixel positions.
- **Condition B:** the original, unpermuted digit images.

For each of five model seeds, an MLP is initialized once and its starting weights are saved.

- The **experienced model** is first trained for 100 epochs on Condition A and is then trained for 40 epochs on Condition B.
- The **fresh model** begins from the same original initialization and is trained directly on Condition B for the same 40-epoch adaptation budget.

A new Adam optimizer is created for the experienced and fresh models during Condition B learning so that optimizer history from Condition A does not explain differences in adaptation.

The analysis focuses on the Stability–Plasticity Diagnostic:

**CHANGE → RETENTION → NEW LEARNING → RATE → TRADEOFF**

The primary evidence is the post-change learning trajectory rather than the immediate performance drop after the condition changes.

## Main Settings

- Python: 3.13.15
- PyTorch: 2.11.0+cpu
- scikit-learn: 1.6.1
- NumPy: 2.1.3
- pandas: 2.2.3
- Device: CPU
- Data split seed: 42
- Pixel permutation seed: 123
- Model seeds: 0, 1, 2, 3, 4
- Condition A pretraining epochs: 100
- Condition B adaptation epochs: 40
- Optimizer: Adam
- Learning rate: 0.001
- Loss: cross-entropy

## Reproduction Instructions

### 1. Open the notebook

Open:

```text
code/ANLY_735_replication_lab02_hany_raza.ipynb
```

in Google Colab or another Python environment with the listed packages installed.

### 2. Run all notebook cells from top to bottom

The notebook:

1. loads and preprocesses the Digits dataset;
2. creates Condition A and Condition B;
3. defines the MLP;
4. trains experienced and fresh models across five paired seeds;
5. records post-change learning trajectories;
6. calculates threshold-based adaptation rates;
7. generates the Stability–Plasticity diagnostic; and
8. exports the data tables and figures.

### 3. Save generated outputs into the repository folders

Place the generated CSV files in:

```text
data/
```

Place the generated PNG files in:

```text
figures/
```

The expected output files are listed in the repository structure above.

### 4. Render the Quarto report

Keep the `.qmd` file and `references.bib` together in the repository root.

From a terminal with Quarto installed, run:

```bash
quarto render ANLY_735_replication_lab_02_completed_with_readme.qmd
```

The report is configured to render to a Word document.

## Key Results From the Submitted Run

Across five model seeds:

- Mean Condition A accuracy before the change: **94.70%**
- Mean Condition B accuracy before adaptation: **6.74%**
- Condition B accuracy after 40 epochs:
  - Experienced model: **66.78%**
  - Fresh model: **81.89%**
- Epochs required to reach 50% Condition B accuracy:
  - Experienced model: **29**
  - Fresh model: **10**
- Experienced model Condition A retention after 40 Condition B epochs: **80.37%**

These results are consistent with reduced post-change learning plasticity over the observed adaptation window: the experienced model continued learning but adapted more slowly than the equivalent fresh model.

## Replication Scope and Verdict

This project is a **proxy replication**. It uses supervised digit classification rather than deep reinforcement learning and tests one controlled type of nonstationarity.

The report therefore uses the verdict:

**Partially Reproduced**

The verdict applies only to the narrower pattern tested here and should not be interpreted as reproducing all findings surveyed by Klein et al. (2024).

## Reference

Klein, T., Miklautz, L., Sidak, K., Plant, C., & Tschiatschek, S. (2024). *Plasticity loss in deep reinforcement learning: A survey*. arXiv:2411.04832. https://arxiv.org/abs/2411.04832
