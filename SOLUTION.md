# An SPM1d-PCA-SVM Framework for Quantifying Dynamic Gait Adaptation

**Author:** Xining Zhang  
**Affiliation:** Shanghai University of Sport, School of Smart Sport Engineering  
**Status:** Accepted — ICBET 2026 (EI-indexed Conference)

---

## 1. Problem Statement

Quantifying the extent of motor pattern restoration following gait retraining is a longstanding challenge in biomechanics. Traditional methods — discrete point analysis and univariate statistical tests — evaluate variables in isolation and focus on magnitude differences at specific time points. This makes them blind to two critical phenomena:

1. **Latent coordination changes**: A runner can replicate the *average magnitude* of a movement while still using a fundamentally different *motor strategy*.
2. **Multi-joint coupling**: Running is a whole-body, non-linear, cyclic task. Analyzing one joint at a time ignores the synergistic interactions across the kinematic chain.

**Core research question:** After 12 weeks of gait retraining from rearfoot to forefoot strike, do runners truly restore a habitual forefoot gait pattern — or do they merely mimic its surface appearance?

---

## 2. Dataset

- **Participants:** 24 male runners
  - HAB group (n=12): Habitual forefoot strikers (≥1 year, no injury history)
  - RET group (n=12): Formerly rearfoot strikers, retrained over 12 weeks to forefoot strike
- **Speed:** 12 km/h on instrumented split-belt treadmill
- **Capture:** 3D motion capture at 200 Hz; ground reaction force at 1000 Hz
- **Features:** 21 kinematic waveforms (7 segments × 3 planes), each time-normalized to 101 points over the stance phase
- **Ethics:** Approved by the Ethics Committee of Shanghai University of Sport (102772024RT012)

**Kinematic channels:**

| Segment | Planes |
|---------|--------|
| Pelvis | Sagittal (X), Frontal (Y), Transverse (Z) |
| Hip | X, Y, Z |
| Thigh | X, Y, Z |
| Knee | X, Y, Z |
| Shank | X, Y, Z |
| Ankle | X, Y, Z |
| Foot | X, Y, Z |

---

## 3. Methodology

### 3.1 Pipeline Overview

```
Raw 3D Kinematics (200 Hz)
        │
        ▼
  Butterworth Filter (4th-order, 10 Hz cutoff)
        │
        ▼
  Stance Phase Extraction (GRF threshold)
        │
        ▼
  Time-normalization → 101 points (0–100% stance)
        │
   ┌────┴────┐
   ▼         ▼
SPM1d     PCA-SVM Pipeline
   │         │
   │    Z-score standardization
   │         │
   │        PCA (95% variance threshold)
   │         │
   │    Linear SVM + LOOCV
   │         │
   │    Feature Back-projection
   │         │
   ▼         ▼
Magnitude   Structural
Differences  Discriminability
        │
        ▼
  Integrated "Magnitude-Structure
   Complementarity" Analysis
```

### 3.2 SPM1d (One-Dimensional Statistical Parametric Mapping)

SPM1d performs topological hypothesis testing over the **entire waveform** rather than at isolated time points, using Random Field Theory to control Type I error inflation.

- **Test:** Independent two-sample t-tests for each kinematic variable
- **Threshold:** α = 0.05
- **Output:** Suprathreshold clusters identifying *when* during stance the groups differ significantly

### 3.3 PCA-SVM Classification Pipeline

**Step 1 — Feature construction:**
- Individual models: 101-dimensional input per joint/plane
- Whole-body model: 2121-dimensional input (21 variables × 101 time points)

**Step 2 — Dimensionality reduction:**
- Z-score standardization across all features
- PCA retaining sufficient components to explain ≥95% cumulative variance

**Step 3 — Classification:**
- Linear SVM (chosen for interpretability in small-sample, high-dimensional settings)
- Leave-One-Out Cross-Validation (LOOCV) for unbiased accuracy estimation

**Step 4 — Feature Back-projection:**

The linear SVM weight vector **W**_svm captures each PC's importance. Projecting back through the PCA loading matrix **L**_pca reconstructs the contribution of each original kinematic variable at each time point:

$$\text{Discriminative Weight}_{i,t} = \sum_{j=1}^{k} \left( W_{\text{svm},j} \times L_{\text{pca},j,i,t} \right)$$

**Contribution Score** per variable = sum of absolute weights across all 101 time points.  
**Discriminative Phase** = time points where absolute weight exceeds the 85th percentile threshold.

---

## 4. Results

### 4.1 SPM1d: Significant Kinematic Differences

| Variable | HAB Mean ± SD | RET Mean ± SD | SPM Result |
|----------|--------------|--------------|------------|
| **Pelvis Sagittal (X)** | -12.3 ± 5.6° | -16.5 ± 3.9° | **p=0.017 (0–100% stance)** |
| **Pelvis Frontal (Y)** | -0.1 ± 1.3° | -1.6 ± 1.8° | **p=0.019 (0–92.1% stance)** |
| **Knee Frontal (Y)** | -3.6 ± 3.7° | -5.9 ± 2.8° | **p=0.031 (65.3–100% stance)** |
| **Foot Transverse (Z)** | -17.2 ± 4.1° | -14.4 ± 3.0° | **p=0.044 (41.0–97.7% stance)** |
| Knee Sagittal (X) | -30.5 ± 4.0° | -29.2 ± 2.9° | N.S. |
| Hip Sagittal (X) | 12.4 ± 4.1° | 12.0 ± 4.0° | N.S. |
| Ankle Sagittal (X) | 5.6 ± 4.0° | 6.6 ± 2.8° | N.S. |

*N.S. = Not Significant (p > 0.05). Bold rows indicate significant differences.*

**Key finding:** SPM1d detected postural changes in the pelvis and foot — the explicit, macro-level adjustments associated with forefoot running instruction. Hip, knee (sagittal), and ankle showed no significant differences.

### 4.2 PCA-SVM: Classification Accuracy

| Model Configuration | Input Dims | Retained PCs | Explained Var | SVM Accuracy |
|--------------------|-----------|-------------|--------------|-------------|
| Pelvis — ALL Planes | 303 | 3 | 96.32% | 68.42% |
| **Knee Transverse (Z)** | 101 | 3 | 98.15% | **78.95%** |
| **Shank Sagittal (X)** | 101 | 3 | 99.25% | **71.05%** |
| Hip — ALL Planes | 303 | 5 | 95.03% | 57.89% |
| Ankle — ALL Planes | 303 | 6 | 95.86% | 65.79% |
| **Whole-body (21 vars)** | 2121 | 15 | 95.74% | **71.05%** |

*Models exceeding 70% accuracy threshold highlighted in bold.*

### 4.3 Feature Back-projection: Whole-body Contribution Scores (Top 10)

| Rank | Variable | Contribution Score | SPM1d Result |
|------|----------|--------------------|-------------|
| 1 | **Knee Sagittal (X)** | **1.99** | **N.S. ← Key discrepancy** |
| 2 | Ankle Sagittal (X) | 1.66 | N.S. |
| 3 | Shank Sagittal (X) | 1.63 | N.S. |
| 4 | Knee Transverse (Z) | 1.38 | N.S. |
| 5 | Foot Transverse (Z) | 0.86 | Significant |
| 6 | Pelvis Transverse (Z) | 0.85 | N.S. |
| 7 | Hip Transverse (Z) | 0.83 | N.S. |
| 8 | Thigh Transverse (Z) | 0.83 | N.S. |
| 9 | Knee Frontal (Y) | 0.81 | Significant |
| 10 | Hip Sagittal (X) | 0.77 | N.S. |

### 4.4 Discriminative Phases

| Model | Key Variable | Discriminative Window |
|-------|--------------|-----------------------|
| Knee Transverse (single-var) | Knee rotation | **0–14% stance** (early weight acceptance) |
| Shank Sagittal (single-var) | Shank angle | **0–14% stance** (early weight acceptance) |
| Whole-body | Knee Sagittal | **16–30% stance** (mid-stance) |

---

## 5. Key Insight: Magnitude-Structure Complementarity

The central finding is a striking **dissociation** between the two methods:

```
                  SPM1d          PCA-SVM
                  ─────          ───────
Pelvis Sagittal   SIGNIFICANT    low contribution
Foot Transverse   SIGNIFICANT    moderate contribution
─────────────────────────────────────────────────────
Knee Sagittal     NOT SIG.  →→   TOP CONTRIBUTOR (1.99)
Shank Sagittal    NOT SIG.  →→   3rd contributor (1.63)
Knee Transverse   NOT SIG.  →→   78.95% accuracy (single-var)
```

**Interpretation:**
- SPM1d detects **explicit postural shifts**: macro-level changes in pelvic tilt and foot placement that result from the conscious retraining instruction ("land on your forefoot, lean forward")
- PCA-SVM detects **implicit motor signatures**: subtle, non-linear coordination patterns in knee-shank coupling that persist even after the surface kinematics appear converged

This proves: **statistical non-significance ≠ full motor restoration.**

The retrained runners successfully replicated the *average magnitude* of knee flexion (evading SPM1d), but their *temporal coordination pattern* remained detectably distinct (captured by SVM at 71.05% accuracy).

---

## 6. Code

The full analysis pipeline is available in the `code/` directory:

- [`code/pca_svm_pipeline.ipynb`](code/pca_svm_pipeline.ipynb) — Data loading, PCA-SVM classification, LOOCV evaluation, and feature back-projection
- [`code/visualization.ipynb`](code/visualization.ipynb) — Concordance/discordance map comparing SPM1d and SVM sensitivity

### Environment

```bash
pip install numpy pandas scikit-learn matplotlib seaborn openpyxl
```

**Key libraries:**
- `scikit-learn` — PCA, SVM, LeaveOneOut cross-validation
- `numpy` / `pandas` — data handling
- `matplotlib` / `seaborn` — visualization

### Quick Start

```python
# In pca_svm_pipeline.ipynb:
# 1. Set DATA_ROOT to your data directory
DATA_ROOT = "path/to/your/data"

# 2. Run all cells sequentially
# Output: classification accuracy, contribution scores, discriminative phase plots
```

> **Note:** The raw motion capture data (`.xlsx` files exported from Visual 3D) are not included in this repository due to participant privacy. The code is fully functional with appropriately structured data following the Visual 3D export format described in the paper.

---

## 7. Conclusion

This project validates the **SPM1d-PCA-SVM** framework as a new paradigm for biomechanical assessment of motor adaptation:

1. **SPM1d** localizes explicit, phase-specific postural shifts (what changed on the surface)
2. **PCA-SVM with feature back-projection** decodes latent motor coordination signatures (what changed beneath the surface)

Together, they form a **"Magnitude-Structure Complementarity"** framework that avoids the Type II error of concluding that gait was fully restored when standard statistics find no significant difference.

**Broader impact:** This approach can be applied to any motor rehabilitation context — ACL recovery, stroke rehabilitation, sports skill acquisition — where distinguishing true motor restoration from surface-level compensation is clinically critical.

---

## 8. Reference

Zhang, X., Zhi, R., & Wu, Y. (2026). An SPM1d-PCA-SVM Framework for Quantifying Dynamic Gait Adaptation: A Validation Study on Forefoot Strike Retraining. *Proceedings of ICBET 2026* (EI-indexed). *(Accepted)*
