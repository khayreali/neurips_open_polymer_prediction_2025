Project Overview
This notebook presents a hybrid machine learning approach for predicting five critical physical properties of polymers from their SMILES molecular representations:

Glass Transition Temperature (Tg)
Fractional Free Volume (FFV)
Crystallization Temperature (Tc)
Density
Radius of Gyration (Rg)

Methodology
Data Enhancement

Integrates multiple external polymer datasets to expand training data from ~8,000 to over 11,000 samples
Implements SMILES augmentation to increase training diversity

Feature Engineering

Molecular Descriptors: RDKit-based chemical properties, Mordred descriptors, and graph-based features
Fingerprints: Morgan (radius=2, 128 bits) and MACCS keys for structural representation
Feature Selection: Variance threshold filtering optimized per target property

Model Architecture
Ensemble Approach:

Tree-based Models: XGBoost + Random Forest trained on molecular descriptors and fingerprints
Graph Neural Network: Pre-trained GNN with Graph Attention layers (GATConv) learning from molecular graph structures
Meta-Model: XGBoost stacking ensemble that optimally combines predictions from both approaches

Performance (Cross-Validation MAE)

Tg: 13.80°C
FFV: 0.0027
Tc: 0.0148
Density: 0.0159 g/cm³
Rg: 0.498 Å
