# NeurIPS 2025 - Open Polymer Prediction

Competition Rank: 306/2285
![Alt text](./images/screenshot.png)

## Overview

This notebook tackles the problem of predicting five physical properties of polymers from SMILES molecular representations. The approach combines tree-based models trained on molecular descriptors with a graph neural network that operates directly on molecular structure.

## Target Properties

- Glass Transition Temperature (Tg)
- Fractional Free Volume (FFV) 
- Crystallization Temperature (Tc)
- Density
- Radius of Gyration (Rg)

## Methods

### Data Processing

The original training set of ~8,000 samples was expanded to 11,382 by incorporating external polymer databases including TgSS, JCIM, and supplementary competition datasets. SMILES strings were validated and canonicalized using RDKit, with augmentation applied during training to generate alternative valid representations.

### Feature Engineering

Two parallel feature extraction pipelines were implemented:

**Molecular Descriptors**: Calculated 300-400 features per target including RDKit descriptors (MolWeight, LogP, TPSA), Mordred descriptors, and graph-based metrics (diameter, cycle count). Feature sets were optimized for each property through variance threshold filtering.

**Fingerprints**: Combined Morgan fingerprints (radius=2, 128 bits) with MACCS keys (167 bits) to capture both local chemical environments and substructure patterns.

### Model Architecture

The solution uses a three-level architecture:

1. **Base Models**: XGBoost and Random Forest trained on descriptor/fingerprint features. Hyperparameters were tuned individually for each target property.

2. **Graph Neural Network**: Pre-trained 3-layer Graph Attention Network with 8 attention heads. The GNN processes molecular graphs directly, with node features encoding atom properties and edge features representing bonds.

3. **Meta-Model**: XGBoost trained on out-of-fold predictions from both base models and GNN to learn optimal combination weights.

### Training Strategy

- 5-fold cross-validation for tree-based models
- 10-fold ensemble for GNN predictions
- Out-of-fold stacking to prevent overfitting in the meta-model

## Results

Cross-validation MAE scores:

| Property | Tree Models Only | Final Ensemble | Improvement |
|----------|-----------------|----------------|-------------|
| Tg | 14.88°C | 13.80°C | 7.3% |
| FFV | 0.0055 | 0.0027 | 50.9% |
| Tc | 0.0181 | 0.0148 | 18.2% |
| Density | 0.0184 g/cm³ | 0.0159 g/cm³ | 13.6% |
| Rg | 0.6798 Å | 0.4977 Å | 26.8% |

The ensemble consistently outperforms individual model components, with particularly strong improvements for FFV and Rg predictions.

## Implementation Notes

The code relies on RDKit 2025.3.3 for molecular processing, PyTorch/PyTorch Geometric for the GNN, and standard ML libraries (XGBoost, scikit-learn) for tree-based models. Mordred is used for advanced descriptor calculation.

Feature importance analysis reveals that molecular weight, topological polar surface area, and rotatable bond count are consistently important across properties, while target-specific features (e.g., fraction of sp3 carbons for density) provide additional predictive power.
