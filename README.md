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

The complete prediction pipeline is:

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
           │              │        ESM-2            │
           │              │   Frozen Protein       │
           │              │      Encoder           │
           │              └───────────┬────────────┘
           │                          │
           │                     480-dim Vector
           │                          │
           └──────────────┬───────────┘
                          │
                          ▼
               ┌─────────────────────┐
               │ Attention-Gated     │
               │ Fusion Module       │
               └──────────┬──────────┘
                          │
                          ▼
               ┌─────────────────────┐
               │     Fusion MLP      │
               │  780 → 512 → 256 →128│
               └──────────┬──────────┘
                          │
              ┌───────────┼────────────┐
              │           │            │
              ▼           ▼            ▼
       ┌────────────┐ ┌────────────┐ ┌────────────────┐
       │ AChE       │ │ Moderate   │ │ High CYP       │
       │ Binding    │ │ PK         │ │ PK Exposure    │
       │ Potency    │ │ Exposure   │ │ Sensitivity    │
       └────────────┘ └────────────┘ └────────────────┘
                          │
                          ▼
                  ┌─────────────────┐
                  │ East Asian      │
                  │ Response        │
                  └─────────────────┘

---

## Motivation

Drug response and safety can vary across populations because genetic
differences can influence the activity of drug-metabolizing enzymes and
transport-related pathways.

For example, the project considers pharmacogenomic variants such as
**CYP2D6*10**, a reduced-activity variant with higher prevalence in East Asian
populations.

Conventional ADMET models generally focus on the molecular characteristics
of the drug:

```text
Drug → Prediction


---

## Then `Problem Statement`

```markdown
---

## Problem Statement

Conventional ADMET prediction approaches often treat drug properties as
independent of patient or population-specific genetic variation.

PopADMET formulates the problem as a multi-input prediction task.

### Inputs

- Drug molecule represented as a **SMILES string**
- Population-specific pharmacogenomic variant information
- Corresponding protein sequence representation

### Outputs

The model simultaneously predicts four ADMET-related endpoints:

1. **AChE Binding Potency**
2. **Moderate CYP-related PK Exposure Sensitivity**
3. **High CYP-related PK Exposure Sensitivity**
4. **East Asian Response Likelihood**

The objective is to learn a relationship between molecular structure,
protein-level information, and pharmacogenomic context for
population-aware prediction.
