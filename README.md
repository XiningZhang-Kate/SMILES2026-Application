# SPM1d-PCA-SVM Gait Adaptation Framework

**SMILES 2026 Application Project**  
**Applicant:** Zhang Xining | Shanghai University of Sport

---

## Overview

This repository contains the application project for the **SMILES 2026 Summer School of Machine Learning** (Skoltech × Nanjing University).

The project presents a machine learning framework for biomechanical analysis of gait adaptation, combining classical statistical methods with modern ML pattern recognition to reveal hidden motor coordination signatures invisible to traditional analysis.

📄 **[Read the full project report → SOLUTION.md](SOLUTION.md)**

---

## Project Summary

**Paper:** *An SPM1d-PCA-SVM Framework for Quantifying Dynamic Gait Adaptation: A Validation Study on Forefoot Strike Retraining*  
**Status:** Accepted at ICBET 2026 (EI-indexed)

### The Problem
After 12 weeks of gait retraining (rearfoot → forefoot running), do runners truly restore a habitual motor pattern — or do they only mimic its surface appearance?

### The Approach

| Method | What it detects |
|--------|----------------|
| **SPM1d** | Explicit, magnitude-level postural shifts (traditional stats over full waveform) |
| **PCA-SVM + Back-projection** | Latent coordination patterns invisible to univariate statistics |

### Key Finding
The **Knee Sagittal Angle** was statistically non-significant in SPM1d (p > 0.05) yet emerged as the **#1 discriminative feature** in the SVM model (Contribution Score: 1.99). This proves that statistical significance ≠ motor pattern restoration.

---

## Repository Structure

```
├── SOLUTION.md                  ← Full project report (required)
├── README.md                    ← This file
└── code/
    ├── pca_svm_pipeline.ipynb   ← Main ML pipeline
    └── visualization.ipynb      ← SPM1d vs SVM comparison plot
```

---

## ML Skills Demonstrated

- Dimensionality reduction (PCA)
- Support Vector Machine classification
- Leave-One-Out Cross-Validation
- Feature interpretability (weight back-projection)
- High-dimensional time-series analysis
- Biomechanical signal processing

---

## Author

**Zhang Xining**  
School of Smart Sport Engineering, Shanghai University of Sport  
23730201@sus.edu.cn
