# PopADMET: Population-Aware Drug Safety and Target Prediction

> A population-aware AI framework for drug-response and safety prediction combining
> molecular, protein, and pharmacogenomic representations.


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
- [Project Workflow](#project-workflow)
- [Results](#results)
- [Evaluation](#evaluation)
- [My Contribution](#my-contribution)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Usage](#usage)
- [Reproducibility](#reproducibility)
- [Future Improvements](#future-improvements)
- [Limitations](#limitations)
- [Acknowledgements](#acknowledgements)
- [Project Report](#project-report)
- [License](#license)

---

## Overview

Drug response and safety can vary across individuals and populations because of
differences in molecular characteristics, biological targets, and genetic variation.

PopADMET explores a population-aware approach to drug-response and safety prediction
by combining:

- Molecular representations of drug compounds
- Protein-level representations
- Pharmacogenomic variant information
- Graph-based deep learning
- Attention-based representation fusion

The framework uses a Graph Isomorphism Network (GIN) to learn molecular
representations and ESM-2 to obtain protein-level representations. These
representations are integrated through an attention-gated fusion mechanism for
downstream prediction tasks.

---

## Motivation

Drug-response and safety prediction can benefit from information available at
multiple biological levels.

Traditional approaches may primarily focus on molecular or chemical properties,
while drug response can also depend on protein targets and population-specific
genetic variation.

PopADMET explores the integration of these complementary information sources
within a deep learning framework.

The central idea is:

Drug Molecular Structure
          +
Protein Representation
          +
Pharmacogenomic Information
          ↓
Representation Integration
          ↓
Attention-Gated Fusion
          ↓
Prediction
          ↓
Drug Safety / Biological Activity



          +

