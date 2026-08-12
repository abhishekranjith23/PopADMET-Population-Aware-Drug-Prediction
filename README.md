# PopADMET: Context-Aware AI for Population-Specific ADMET Prediction

> A context-aware AI framework for population-specific ADMET prediction
> targeting Acetylcholinesterase (AChE), combining drug molecular structure
> with pharmacogenomic variant information.

PopADMET is a population-aware multi-task AI framework designed to predict
drug-response and safety characteristics while accounting for population-
specific genetic variation.

Unlike conventional ADMET models that assign a single prediction to a drug,
PopADMET jointly considers the **drug molecular structure** and an
**East Asian pharmacogenomic variant profile** to produce population-specific
predictions.

The system combines a **GIN-based chemical encoder**, a **frozen ESM-2 protein
language model**, and an **attention-gated fusion mechanism** to generate four
ADMET prediction scores.

---

## Table of Contents

- [Overview](#overview)
- [Motivation](#motivation)
- [Problem Statement](#problem-statement)
- [Objectives](#objectives)
- [Scope](#scope)
- [Key Features](#key-features)
- [Population and Genetic Variants](#population-and-genetic-variants)
- [Dataset](#dataset)
- [System Architecture](#system-architecture)
- [Methodology](#methodology)
  - [1. Data Collection](#1-data-collection)
  - [2. Data Preprocessing](#2-data-preprocessing)
  - [3. Molecular Graph Construction](#3-molecular-graph-construction)
  - [4. Protein Variant Representation](#4-protein-variant-representation)
  - [5. Dynamic PK Risk Scoring](#5-dynamic-pk-risk-scoring)
  - [6. Attention-Gated Fusion](#6-attention-gated-fusion)
  - [7. Multi-Task Prediction](#7-multi-task-prediction)
  - [8. Gradual Encoder Unfreezing](#8-gradual-encoder-unfreezing)
- [Model Architecture](#model-architecture)
- [Prediction Tasks](#prediction-tasks)
- [Training Configuration](#training-configuration)
- [Evaluation Metrics](#evaluation-metrics)
- [Results](#results)
- [Ablation Study](#ablation-study)
- [Clinical Case Studies](#clinical-case-studies)
- [Per-Variant Analysis](#per-variant-analysis)
- [Attention Analysis](#attention-analysis)
- [Deployment](#deployment)
- [Technology Stack](#technology-stack)
- [Project Workflow](#project-workflow)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Limitations](#limitations)
- [Future Scope](#future-scope)
- [Acknowledgements](#acknowledgements)
- [Project Report](#project-report)
- [References](#references)
- [License](#license)

---

## Overview

Drug safety and response can vary between individuals because of differences
in genetic background and metabolic enzyme activity.

Many conventional ADMET prediction systems primarily model properties of the
drug molecule itself. Such systems may therefore produce the same prediction
for different patients even when genetic variation can affect drug metabolism
and exposure.

PopADMET addresses this problem by incorporating both:

- **Drug molecular structure**
- **Population-specific pharmacogenomic variant information**

The framework focuses on **Acetylcholinesterase (AChE)-targeting drugs**
relevant to Alzheimer's disease and models five East Asian pharmacogenomic
variants.

The overall prediction pipeline combines molecular and protein-level
representations before performing multi-task prediction.

---

## Motivation

Drug response and safety can vary across populations because genetic
differences can influence the activity of drug-metabolizing enzymes and
transport-related pathways.

For example, the project considers pharmacogenomic variants such as
`CYP2D6*10`, a reduced-activity variant with higher prevalence in East Asian
populations.

Conventional ADMET models generally focus primarily on the molecular
characteristics of the drug:

```text
Drug → Prediction
```

This can result in the same prediction being assigned to different
individuals or populations.

PopADMET instead explores a context-aware formulation:

```text
Drug Molecular Structure
          +
Pharmacogenomic Context
          ↓
Population-Specific Prediction
```

The motivation of the project is therefore to investigate whether combining
molecular, protein-level, and pharmacogenomic information can provide more
context-aware predictions for drug response and safety.

---

## Problem Statement

Conventional ADMET prediction approaches often treat drug properties as
independent of patient or population-specific genetic variation.

PopADMET formulates the problem as a multi-input, multi-task prediction
problem.

### Inputs

- Drug molecule represented as a **SMILES string**
- Population-specific pharmacogenomic variant information
- Corresponding protein sequence representation

### Outputs

The model predicts four ADMET-related endpoints:

1. **AChE Binding Potency**
2. **Moderate CYP-related PK Exposure Sensitivity**
3. **High CYP-related PK Exposure Sensitivity**
4. **East Asian Response Likelihood**

The objective is to learn relationships between molecular structure,
protein-level information, and pharmacogenomic context for
population-aware prediction.

---

## Objectives

The main objectives of PopADMET are:

- Develop a population-aware multi-task prediction framework.
- Represent drug molecules using molecular graphs and a GIN-based encoder.
- Generate protein-level representations using the frozen ESM-2 model.
- Integrate molecular and protein representations using attention-gated
  fusion.
- Incorporate pharmacogenomic information into drug-response prediction.
- Train the system across four ADMET-related prediction endpoints.
- Apply gradual unfreezing of the chemical encoder during training.
- Evaluate the model using molecule-level train, validation, and test splits.
- Analyse model behaviour across pharmacogenomic variants.
- Validate the framework using selected Alzheimer's disease drugs.
- Provide an interactive interface for model inference.

---

## Scope

The scope of PopADMET includes:

- Molecular representation learning
- Protein representation learning
- Pharmacogenomic information integration
- Population-aware drug-response prediction
- ADMET-related multi-task prediction
- AChE-related drug activity prediction
- CYP-related pharmacokinetic risk prediction
- Analysis of population-specific response patterns
- Interactive model inference

The framework is intended as a research and prediction system and is not
designed to replace clinical decision-making or validated clinical
diagnostic systems.

---

## Key Features

- **Population-aware prediction** using pharmacogenomic context
- **Graph-based molecular representation** using Graph Isomorphism Networks
  (GIN)
- **Protein language model representations** using ESM-2
- **Attention-gated representation fusion**
- **Multi-task prediction** across four ADMET-related endpoints
- **Pharmacogenomic variant analysis**
- **Gradual chemical encoder unfreezing**
- **Molecule-level data splitting**
- **Evaluation using multiple classification metrics**
- **Case-study analysis using Alzheimer's-related drugs**
- **Interactive model deployment through Gradio**

---

## Population and Genetic Variants

The framework focuses on an **East Asian pharmacogenomic context** and
incorporates five variants considered in the project:

- `CYP2D6*10`
- `CYP2D6*1`
- `CYP3A5*3`
- `CYP3A5*1`
- `BCHE*K`

These variants are incorporated to represent genetic factors that may affect
drug metabolism, pharmacokinetic exposure, and drug-response behaviour.

The pharmacogenomic information is incorporated into the model through
protein-level representations generated using ESM-2.

---

## Dataset

The project constructs a pharmacogenomically grounded dataset containing
**20,248 drug-variant pairs**.

The dataset combines:

- Drug molecular information
- SMILES representations
- Protein or variant sequence information
- Pharmacogenomic information
- AChE-related activity information
- CYP-related pharmacokinetic information
- Population-specific response information

### Data Splitting

The evaluation framework uses **molecule-level train, validation, and test
splits** to reduce information leakage between structurally related samples
across different partitions.

---

## System Architecture

The system consists of two main representation-learning branches:

1. **Chemical representation branch**
2. **Protein/pharmacogenomic representation branch**

The two representations are subsequently integrated through an
attention-gated fusion module.

```text
                         ┌─────────────────────┐
                         │     Drug SMILES     │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │  Molecular Graph    │
                         │       RDKit         │
                         └──────────┬──────────┘
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │ GIN Chemical        │
                         │ Encoder             │
                         └──────────┬──────────┘
                                    │
                              300-dim Vector
                                    │
                                    │
                                    │
                                    │              ┌────────────────────────┐
                                    │              │ East Asian Variant     │
                                    │              │ Protein Sequence       │
                                    │              └───────────┬────────────┘
                                    │                          │
                                    │                          ▼
                                    │              ┌────────────────────────┐
                                    │              │         ESM-2           │
                                    │              │   Frozen Encoder       │
                                    │              └───────────┬────────────┘
                                    │                          │
                                    │                     480-dim Vector
                                    │                          │
                                    └──────────────┬───────────┘
                                                   │
                                                   ▼
                                      ┌────────────────────────┐
                                      │ Attention-Gated Fusion  │
                                      │        Module            │
                                      └────────────┬───────────┘
                                                   │
                                                   ▼
                                      ┌────────────────────────┐
                                      │       Fusion MLP        │
                                      │   780 → 512 → 256 →128 │
                                      └────────────┬───────────┘
                                                   │
                            ┌──────────────────────┼──────────────────────┐
                            │                      │                      │
                            ▼                      ▼                      ▼
                   ┌────────────────┐    ┌────────────────┐    ┌────────────────┐
                   │ AChE Binding    │    │ Moderate CYP  │    │ High CYP       │
                   │ Potency         │    │ PK Exposure   │    │ PK Exposure    │
                   └────────────────┘    │ Sensitivity   │    │ Sensitivity    │
                                         └────────────────┘    └────────────────┘
                                                   │
                                                   ▼
                                         ┌────────────────────┐
                                         │ East Asian         │
                                         │ Response Likelihood│
                                         └────────────────────┘
```

---

## Methodology

### 1. Data Collection

The project combines molecular, protein, and pharmacogenomic information to
construct a population-aware prediction dataset.

The major information sources include:

- Drug molecular structures
- SMILES representations
- Pharmacogenomic variants
- Protein sequences
- AChE-related activity information
- CYP-related pharmacokinetic information

The resulting dataset contains **20,248 drug-variant pairs**.

---

### 2. Data Preprocessing

The preprocessing stage prepares molecular and pharmacogenomic information
for model training.

The major steps include:

- Processing molecular SMILES strings
- Validating molecular structures
- Constructing molecular graphs
- Preparing protein/variant sequences
- Organising drug-variant pairs
- Preparing the four prediction targets
- Creating molecule-level train, validation, and test partitions

---

### 3. Molecular Graph Construction

Drug molecules are represented as molecular graphs.

Using RDKit, molecular structures are converted from SMILES representations
into graph-based representations consisting of:

- Nodes representing atoms
- Edges representing chemical bonds
- Molecular connectivity information

These graphs are processed by a **Graph Isomorphism Network (GIN)** to learn
molecular-level embeddings.

The chemical encoder produces a **300-dimensional molecular representation**.

---

### 4. Protein Variant Representation

Protein-level information associated with the pharmacogenomic variants is
represented using **ESM-2**, a protein language model.

The ESM-2 encoder generates protein-level embeddings from the corresponding
protein sequences.

The project uses the ESM-2 representation as a protein-level feature
representation before integrating it with the molecular representation.

The resulting representation used in the fusion stage is **480-dimensional**.

---

### 5. Dynamic PK Risk Scoring

Pharmacokinetic information is incorporated into the prediction framework
through CYP-related exposure categories.

The model considers:

- Moderate CYP-related PK exposure sensitivity
- High CYP-related PK exposure sensitivity

These tasks provide a representation of pharmacokinetic risk associated with
the drug and pharmacogenomic context.

---

### 6. Attention-Gated Fusion

The molecular and protein representations are combined through an
**attention-gated fusion mechanism**.

The fusion mechanism learns how much information to retain from the
different representation sources rather than simply concatenating them.

Conceptually:

```text
Molecular Representation
          +
Protein Representation
          ↓
Attention-Gated Fusion
          ↓
Joint Representation
```

The fused representation is passed through a multi-layer perceptron:

```text
780 → 512 → 256 → 128
```

The resulting shared representation is used by the downstream prediction
heads.

---

### 7. Multi-Task Prediction

PopADMET uses a multi-task learning setup to predict four related
ADMET-related endpoints simultaneously:

```text
                    Shared Representation
                            │
             ┌──────────────┼──────────────┐
             │              │              │
             ▼              ▼              ▼
          AChE           Moderate        High CYP
         Potency          CYP PK           PK
             │              │              │
             └──────────────┴──────────────┘
                            │
                            ▼
                  East Asian Response
```

Learning multiple related objectives allows the framework to use shared
representations across the prediction tasks.

---

### 8. Gradual Encoder Unfreezing

The chemical encoder is initially controlled during training to reduce
instability in the early stages of optimization.

The project applies **gradual unfreezing** of the chemical encoder so that
the molecular representation can progressively adapt to the downstream
prediction objectives.

This provides a controlled approach to fine-tuning the learned molecular
representation.

---

## Model Architecture

The model contains the following major components:

### Chemical Encoder

- Molecular graph input
- GIN-based graph neural network
- 300-dimensional molecular embedding

### Protein Encoder

- Variant-associated protein sequence
- Frozen ESM-2 encoder
- 480-dimensional protein representation

### Fusion Module

- Attention-gated fusion
- Combined molecular and protein information
- Fusion MLP

### Prediction Module

The shared representation is used to generate four prediction outputs:

- AChE binding potency
- Moderate CYP PK exposure sensitivity
- High CYP PK exposure sensitivity
- East Asian response likelihood

---

## Prediction Tasks

### 1. AChE Binding Potency

Predicts the activity associated with interaction with
**Acetylcholinesterase (AChE)**.

### 2. Moderate CYP PK Exposure Sensitivity

Predicts the moderate CYP-related pharmacokinetic exposure category.

### 3. High CYP PK Exposure Sensitivity

Predicts the high CYP-related pharmacokinetic exposure category.

### 4. East Asian Response Likelihood

Predicts response likelihood within the population-specific context used by
the project.

---

## Training Configuration

The training process combines molecular graph learning, protein
representations, and multi-task prediction.

Major training components include:

- GIN-based molecular encoder
- Frozen ESM-2 protein representation
- Attention-gated fusion
- Shared fusion MLP
- Multi-task prediction heads
- Gradual unfreezing of the chemical encoder
- Molecule-level train/validation/test splitting

The exact hyperparameters and training configuration should be maintained
in the corresponding training scripts and notebooks in the repository.

---

## Evaluation Metrics

The model is evaluated using classification-oriented metrics appropriate
for the prediction tasks.

The evaluation framework includes:

- **ROC-AUC**
- **Accuracy**
- **Precision**
- **Recall**
- **F1-score**
- **Confusion matrices**

ROC-AUC is particularly important for comparing model discrimination across
the prediction tasks.

---

## Results

The project achieved the following reported performance:

| Prediction Task | ROC-AUC |
|---|---:|
| Population-specific toxicity prediction | **0.848** |
| AChE activity prediction | **0.823** |

The reported results demonstrate the ability of the framework to learn from
combined molecular and pharmacogenomic information.

Additional task-specific metrics can be added here when the final evaluation
tables are available.

---

## Ablation Study

The project evaluates the contribution of different representation and
fusion components to the overall framework.

The major components considered for analysis include:

- Molecular representation
- Protein representation
- Pharmacogenomic context
- Attention-gated fusion
- Multi-task learning

The purpose of the ablation analysis is to understand how the different
components contribute to prediction performance.

Detailed ablation results should be added from the final experimental
results in the project report.

---

## Clinical Case Studies

The framework is evaluated using selected Alzheimer's disease drugs as
case studies.

The drugs considered include:

- **Donepezil**
- **Rivastigmine**
- **Galantamine**

These case studies are used to examine how the model produces predictions
under the population-specific pharmacogenomic context.

The case-study results should be interpreted as model outputs for research
evaluation and not as clinical recommendations.

---

## Per-Variant Analysis

The model can be analysed across the pharmacogenomic variants used in the
project.

The analysis focuses on differences in prediction behaviour associated with:

- `CYP2D6*10`
- `CYP2D6*1`
- `CYP3A5*3`
- `CYP3A5*1`
- `BCHE*K`

Per-variant analysis helps examine whether the population-specific context
changes model predictions for the same or related drug molecules.

---

## Attention Analysis

The attention-gated fusion mechanism provides a way to analyse how molecular
and protein-level information contributes to the combined representation.

The analysis can be used to investigate:

- Relative contribution of molecular features
- Relative contribution of protein-level features
- Changes in representation across variants
- Behaviour of the fusion mechanism across prediction tasks

This provides additional insight into the behaviour of the multi-input
architecture beyond the final prediction scores.

---

## Deployment

The trained model is designed to support interactive inference through a
**Gradio interface**.

The deployment workflow accepts the required molecular and
pharmacogenomic inputs and produces the corresponding model predictions.

Conceptually:

```text
User Input
    │
    ├── Drug SMILES
    │
    └── Pharmacogenomic Context
            │
            ▼
      PopADMET Model
            │
            ▼
   Prediction Outputs
```

The interface is intended for research and demonstration purposes.

---

## Technology Stack

### Core Frameworks & Libraries

- **PyTorch** — Deep learning framework for model training and inference
- **PyTorch Geometric (PyG)** — Graph neural network library used for GIN
- **ESM-2** — Protein language model used for protein representations
- **scikit-learn** — Evaluation metrics and machine learning utilities

### Molecular Processing

- **RDKit** — Cheminformatics toolkit for molecular structure handling and
  SMILES processing
- **NetworkX** — Graph manipulation and analysis

### Data Handling

- **Pandas** — Data manipulation and analysis
- **NumPy** — Numerical computing

### Development

- **Python 3.8+**
- **Conda / pip**
- **Jupyter Notebook**

### Bioinformatics

- **VEP (Variant Effect Predictor)** —

### Visualization and Experiment Tracking

- **Matplotlib**
- **Seaborn**
- **Weights & Biases (W&B)** where used for experiment tracking

### Deployment

- **Gradio** — Interactive model interface

---

## Project Workflow

The complete workflow can be summarised as:

```text
Data Collection
      │
      ▼
Data Preprocessing
      │
      ├──────────────────────┐
      ▼                      ▼
Molecular Graph         Protein Variant
Construction            Representation
      │                      │
      ▼                      ▼
GIN Encoder              ESM-2 Encoder
      │                      │
      └──────────┬───────────┘
                 │
                 ▼
        Attention-Gated Fusion
                 │
                 ▼
             Fusion MLP
                 │
                 ▼
        Multi-Task Prediction
                 │
        ┌────────┼─────────┐
        ▼        ▼         ▼
      AChE     CYP PK    Response
      Task      Tasks      Task
                 │
                 ▼
             Evaluation
                 │
                 ▼
              Gradio
             Interface
```

---

## Project Structure

The repository structure is:

```text
PopADMET-Population-Aware-Drug-Prediction/
│
├── README.md
├── .gitignore
│
├── data/
│   ├── raw/
│   ├── processed/
│   └── README.md
│
├── notebooks/
│   ├── data_preprocessing.ipynb
│   ├── molecular_graphs.ipynb
│   ├── esm2_embeddings.ipynb
│   ├── model_training.ipynb
│   └── evaluation.ipynb
│
├── src/
│   ├── data/
│   ├── models/
│   ├── preprocessing/
│   ├── training/
│   └── evaluation/
│
├── deployment/
│   └── app.py
│
├── results/
│   ├── metrics/
│   ├── plots/
│   └── predictions/
│
├── docs/
│   └── PopADMET_Report.pdf
│
└── requirements.txt
```


---

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/abhishekranjith23/PopADMET-Population-Aware-Drug-Prediction.git
cd PopADMET-Population-Aware-Drug-Prediction
```

### 2. Create a Conda Environment

```bash
conda create -n popadmet python=3.8
conda activate popadmet
```

### 3. Install Dependencies

If a `requirements.txt` file is provided:

```bash
pip install -r requirements.txt
```

For PyTorch Geometric and ESM-2, install the versions compatible with the
selected PyTorch environment.

---

## Usage

### Data Preparation

Prepare the molecular and pharmacogenomic datasets according to the
preprocessing workflow.

```bash
python src/data/preprocess.py
```

### Model Training

Train the PopADMET model using the training pipeline:

```bash
python src/training/train.py
```

### Evaluation

Evaluate the trained model:

```bash
python src/evaluation/evaluate.py
```

### Interactive Inference

Launch the Gradio interface:

```bash
python deployment/app.py
```

The exact commands should be updated to match the filenames and entry points
present in the final repository.

---

## Limitations

The current framework has several limitations:

- The project focuses on a specific population context rather than providing
  universal population coverage.
- The pharmacogenomic representation is limited to the variants included in
  the dataset.
- Prediction performance depends on the quality and coverage of the
  underlying molecular and pharmacogenomic data.
- Model predictions should not be interpreted as clinical recommendations.
- The framework requires further validation on independent external datasets.
- Additional population groups and genetic variants would be required for
  broader generalisation.

---

## Future Scope

Potential future improvements include:

- Extending the framework to additional populations.
- Incorporating a larger set of pharmacogenomic variants.
- Incorporating additional protein and pathway information.
- Evaluating additional ADMET endpoints.
- Improving external validation using independent datasets.
- Exploring alternative protein language models.
- Investigating more advanced multimodal fusion mechanisms.
- Improving interpretability of molecular and pharmacogenomic predictions.
- Expanding the deployment interface for research-oriented use.

---

## Acknowledgements

This project was developed as an academic/research project involving the
application of deep learning, graph neural networks, protein language
models, and pharmacogenomic data for population-aware drug prediction.

The project makes use of open-source frameworks and libraries including
PyTorch, PyTorch Geometric, RDKit, ESM-2, scikit-learn, Pandas, NumPy, and
Gradio.

---

## Project Report

The complete technical project report provides detailed information about
the problem formulation, dataset construction, methodology, architecture,
training procedure, experiments, evaluation, and results.

If the report is included in the repository, place it under:

```text
PopADMET_Project_Report.pdf
```

and link it below:

**[View the Complete Project Report](PopADMET_Project_Report.pdf)**

---

## References

The project is based on concepts and tools related to:

- Graph Neural Networks and Graph Isomorphism Networks
- Protein language models and ESM-2
- Molecular representation learning
- Pharmacogenomics
- ADMET prediction
- Multi-task learning
- Attention-based feature fusion

Specific papers, datasets, and software references should be listed here
according to the references cited in the final project report.

---

## License

This repository is intended for academic and research purposes.


