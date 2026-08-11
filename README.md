# PopADMET: Context-Aware AI for Population-Specific ADMET Prediction

> A context-aware AI framework for population-specific ADMET prediction targeting
> Acetylcholinesterase (AChE), combining drug molecular structure with
> pharmacogenomic variant information.

PopADMET is a population-aware multi-task AI framework designed to predict
drug-response and safety characteristics while accounting for patient-specific
genetic variation.

Unlike conventional ADMET models that assign a single prediction to a drug,
PopADMET jointly considers the **drug molecular structure** and an **East Asian
pharmacogenomic variant profile** to produce population-specific predictions.

The system combines a **GIN-based molecular encoder**, **ESM-2 protein language
model**, and an **attention-gated fusion mechanism** to generate four ADMET
prediction scores.

---

## Table of Contents

- [Overview](#overview)
- [Motivation](#motivation)
-The project focuses on the fact that drug metabolism can vary across
populations because of genetic differences in metabolic enzymes.

For example, the report highlights CYP2D6*10, a reduced-activity variant
that is considerably more frequent in East Asian populations than in European
populations.

This creates a challenge for genetics-blind ADMET models:

Traditional ADMET

Drug → Prediction

Same drug → Same prediction

Whereas PopADMET aims to model:

Drug + Genetic Variant → Population-Specific Prediction

The project therefore explores whether pharmacogenomic context can improve
early-stage drug safety and response prediction.
- [Problem Statement](#problem-statement)
- Given:

A drug molecule represented as a SMILES string
A patient's East Asian genetic variant profile

design a population-aware AI model that simultaneously predicts:

AChE inhibitory activity
Moderate CYP-related exposure sensitivity
High CYP-related exposure sensitivity
East Asian response likelihood

while accounting for pharmacogenomic variants such as:

CYP2D6*10
CYP2D6*1
CYP3A5*3
CYP3A5*1
BCHE*K
- [Objectives](#objectives)
- The main objectives of PopADMET are:

Build a dual-encoder architecture that processes drug molecular graphs and
variant protein sequences.
Construct a pharmacogenomically grounded dataset containing
20,248 drug-variant pairs.
Use a GIN-based molecular encoder to learn chemical representations.
Use ESM-2 to generate protein-level representations of genetic variants.
Develop an attention-gated fusion mechanism to combine chemical and genetic
information.
Train the system using four ADMET prediction endpoints.
Apply gradual unfreezing of the chemical encoder to improve model learning.
Evaluate the model using molecule-level train, validation, and test splits.
Validate the system using known Alzheimer's drugs including:
Donepezil
Rivastigmine
Galantamine
Deploy the trained model through an interactive Gradio interface.
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

# Overview

Drug safety and response can vary between individuals because of differences in
genetic background and metabolic enzyme activity.

Many conventional ADMET prediction systems primarily model properties of the
drug molecule itself. Such systems may therefore produce the same prediction
for different patients even when genetic variation can affect drug metabolism
and exposure.

PopADMET addresses this problem by incorporating both:

- **Drug molecular structure**
- **Patient-specific pharmacogenomic variant information**

The framework focuses on **Acetylcholinesterase (AChE)-targeting drugs** relevant
to Alzheimer's disease and models five East Asian pharmacogenomic variants.

The system uses:

```text
Drug SMILES
     │
     ▼
GIN Chemical Encoder
     │
     │ 300-dimensional
     ▼
                         ┌─────────────────────┐
                         │ Attention-Gated     │
                         │ Fusion Module       │
                         └──────────┬──────────┘
                                    │
                                    ▼
                              Fusion MLP
                                    │
                                    ▼
                         Shared 128-dim Vector
                                    │
                  ┌─────────────────┼─────────────────┐
                  ▼                 ▼                 ▼
             AChE Binding     PK Exposure      East Asian
               Potency          Prediction        Response


