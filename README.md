# PopADMET: Population-Aware Drug Safety and Target Prediction

A population-aware AI framework for drug-response and safety prediction that combines
molecular representations with pharmacogenomic variant information.

The project explores the integration of molecular and protein-level representations with
population-specific genetic information to improve prediction of drug-related properties
and biological activity.


## Table of Contents

- [Overview](#overview)
- [Motivation](#motivation)
- [Objectives](#objectives)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Methodology](#methodology)
  - [1. Molecular Representation](#1-molecular-representation)
  - [2. Protein Representation](#2-protein-representation)
  - [3. Representation Integration](#3-representation-integration)
  - [4. Pharmacogenomic Information](#4-pharmacogenomic-information)
  - [5. Prediction Tasks](#5-prediction-tasks)
- [Model Architecture](#model-architecture)
- [Technology Stack](#technology-stack)
- ## Technology Stack

### Core Frameworks & Libraries
- **PyTorch** - Deep learning framework for model training and inference
- **PyTorch Geometric (PyG)** - Graph neural network library for GIN implementation
- **ESM-2** (Meta) - Protein language model for generating protein embeddings
- **scikit-learn** - Machine learning utilities for evaluation metrics

### Molecular Processing
- **RDKit** - Cheminformatics toolkit for molecular structure handling and SMILES parsing
- **NetworkX** - Graph manipulation and analysis

### Data Handling
- **Pandas** - Data manipulation and analysis
- **NumPy** - Numerical computing

### Development & Deployment
- **Python 3.8+** - Programming language
- **Conda/pip** - Package management
- **Jupyter Notebook** - Interactive development

### Bioinformatics Tools
- **VEP (Variant Effect Predictor)** - For pharmacogenomic variant annotation 

- [Project Workflow](#project-workflow)
- [Results](#results)
- [Evaluation](#evaluation)
- [My Contribution](#my-contribution)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Future Improvements](#future-improvements)
- [Limitations](#limitations)
- [Acknowledgements](#acknowledgements)
- [License](#license)


 Overview

Drug response and safety can vary across individuals and populations because of
differences in molecular characteristics, biological targets, and genetic variation.

PopADMET explores a population-aware approach to drug-response and safety prediction
by combining:

- Molecular representations of drug compounds
- Protein representations
- Pharmacogenomic variant information
- Deep learning-based representation learning
- Attention-based feature fusion

The framework uses a Graph Isomorphism Network (GIN) to encode molecular structures
and ESM-2 to obtain protein-level representations. These representations are integrated
through an attention-gated fusion mechanism for downstream prediction tasks.



 Motivation

Traditional drug property prediction approaches often focus primarily on molecular
or chemical information. However, drug response and safety can also be influenced by
biological targets and population-specific genetic variation.

This project investigates whether combining these sources of information can provide
a more informative representation for drug-related prediction tasks.

The main idea is:

Drug Molecular Structure
          +
Protein Representation
          +
Pharmacogenomic Information
          ↓
   Feature Integration
          ↓
 Attention-Gated Fusion
          ↓
   Prediction Models
          ↓
Drug Safety / Activity Prediction
