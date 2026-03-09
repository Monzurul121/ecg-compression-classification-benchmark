# ECG Compression–Classification Benchmark

This repository presents an ongoing research project investigating how different ECG compression techniques affect downstream arrhythmia classification performance in a realistic end-to-end telemedicine workflow.

## Project Overview

ECG signal compression often offers a means to mitigate storage problems, transmission cost, privacy issues, and may also support more efficient ML based ECG classification model on edge devices. 

In the conventional workflow, the ECG waveform is compressed and later reconstructed before downstream analysis. However, ECG compression schemes may compromise clinically significant morphology and may also introduce computational burden. When the compressed-reconstructed ECG signal is used in downstream ML classifiers, the classification performance may degrade. This issue is particularly important in clinical applications, where even minor signal distortions could potentially mask subtle but diagnostically significant features.

Most existing ECG compression studies mainly focus on improving compression metrics such as CR, PRD, PRDN, WEDD, and QS, but do not examine how compressed-reconstructed signals affect downstream classification models. Similarly, most ECG classification studies focus on improving classification metrics such as F1-score, accuracy, or AUC using uncompressed raw ECG signals. In practice, however, compression and classification are often used together, especially in wearable devices and telemedicine systems.

This project is designed to address that gap by evaluating ECG compression and downstream classification together in a realistic telemedicine-oriented setting.

## Objective

The main objective of this experiment is to find how different ECG compression techniques impact deep learning-based and non-deep-learning-based downstream ECG classification models in a realistic end-to-end telemedicine scenario.

More specifically, the study investigates:
- how different ECG compression methods affect reconstructed ECG signals,
- how the reconstructed signals influence downstream arrhythmia classification,
- how the effect differs between deep learning-based and traditional classification pipelines,
- and how compression efficiency relates to downstream classification performance.

## Experimental Design

To practically examine the interaction between ECG compression and ECG classification from the perspective of a realistic telemedicine scenario, ECG signals are compressed using four different techniques, and the corresponding reconstructed signals are evaluated using two representative downstream classification models across three experimental scenarios.

### Compression techniques
The benchmark includes four ECG compression methods:
- Near-lossless compression
- Wavelet-based compression
- Deep learning-based compression
- Hybrid compression

### Compression levels
For the **lossy compression methods** (wavelet-based, deep learning-based, and hybrid compression), each technique is evaluated at **three distinct distortion levels**:
- **MILD**: PRD range 4–5
- **MOD**: PRD range 10–12
- **AGGR**: PRD range 20–25

For the **near-lossless compression method**, a **single mild operating point** is used:
- **MILD**: PRD around 0.5

### Downstream classification models
The reconstructed ECG signals are evaluated using two distinct classifiers:
- a **CNN-based deep learning classifier**
- a **traditional RR-based non-deep-learning classifier**

### Evaluation scenarios
Both classification pipelines are evaluated in three scenarios:
1. **Baseline:** training and testing on original ECG signals
2. **Original-to-reconstructed:** training on original signals and testing on compressed-reconstructed signals
3. **Reconstructed-to-reconstructed:** training and testing on compressed-reconstructed signals

## Datasets

The primary experiments are conducted on the **MIT-BIH Arrhythmia Database**.

A limited validation is planned using the **second lead of the INCART 12-lead Arrhythmia Database**.

## Evaluation Metrics

### Compression metrics
Compression performance is evaluated using:
- **CR**
- **PRD**
- **WEDD**

### Classification metrics
Classification performance is evaluated using:
- **Macro-F1**
- **End to End Accuracy**
- **Macro-AUC**
- **Macro-AUPRC (One-vs-Rest)**
- **F1-score for Supraventricular (S) beats**
- **Class-specific ROC-AUC for N, S, and V classes**

The study also analyzes the correlation between compression efficiency and classification accuracy across different experimental settings and presents the compression-classification interaction through graphs and relevant visualizations.

## Workflow and Methodological Focus

In all compression experiments, ECG signals are first standardized through preprocessing and resampling, then compressed, stored as payload, and reconstructed in waveform form. The reconstructed signals are then evaluated using downstream classification models. This workflow reflects a realistic telemedicine setting, where compressed ECG data are transmitted or stored prior to analysis.

A rigorous experimental framework is used to ensure valid comparisons between compression modalities and classification architectures. The protocol emphasizes:

- **Sequential processing:** compression and classification are evaluated as separate stages in a realistic transmission-storage-diagnosis workflow.
- **Data leakage prevention:** inter-patient train-test separation is maintained in both the compression and classification experiments.
- **Standardized categorization:** a three-class AAMI-compliant mapping is used for arrhythmia classification tasks.
- **Unified compression accounting:** common definitions of CR and PRD are enforced across all four compression techniques to maintain fair benchmarking and cross-comparability.

The experiments are performed record-wise, and CR and PRD are computed over the entire standardized ECG record rather than beat-by-beat, to make distortion measures comparable across methods.

## Public Release Note

This public repository is intended for academic visibility and project presentation.

It currently contains the project overview, methodology summary, and selected research materials from an ongoing study. The complete experimental pipeline is not included in this public version because the associated manuscript is still in preparation.

## Project Status

- Literature review completed
- Core benchmark design implemented
- Primary experiments completed / in refinement
- Selected results being organized for presentation
- Manuscript in preparation for journal submission

## Planned Repository Contents

This repository will gradually include:
- project overview documents
- methodology notes
- selected figures and result summaries
- a results-oriented notebook or presentation material
- additional public-facing research materials

## Copyright Notice

Copyright © 2026 Monzurul Islam. All rights reserved.

This repository is shared for academic visibility and discussion purposes only. Reuse, redistribution, or derivative publication of the project materials without explicit permission is not allowed.
