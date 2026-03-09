# ECG Compression–Classification Benchmark

This repository presents an overview of an ongoing research project investigating how different ECG compression techniques affect downstream arrhythmia classification performance in realistic telemedicine workflows.

## Project Overview

ECG compression is widely used to reduce storage and transmission burden in wearable monitoring and telemedicine systems. In practice, ECG signals are compressed, transmitted or stored, reconstructed, and then analyzed by downstream machine learning models. However, compression-induced distortion may alter diagnostically important waveform morphology and degrade classification performance.

This project studies that interaction directly by benchmarking how reconstructed ECG signals affect downstream arrhythmia classification under a realistic end-to-end telemedicine-oriented workflow.

## Objective

The main objective of this study is to evaluate how different ECG compression techniques influence downstream ECG arrhythmia classification performance in a realistic telemedicine scenario.

More specifically, the study examines:
- how compression-induced distortion affects reconstructed ECG signals,
- how this distortion propagates into downstream classifiers,
- whether the effect differs between deep learning-based and traditional classification pipelines,
- and how compression efficiency relates to classification degradation.

## Experimental Design

The benchmark combines:

- **4 ECG compression techniques**
  - Near-lossless compression
  - Wavelet-based compression
  - Deep learning-based compression
  - Hybrid compression

- **2 downstream classification pipelines**
  - CNN-based deep learning classifier
  - Traditional RR-based classifier

- **3 evaluation scenarios**
  1. Train and test on original ECG signals
  2. Train on original signals and test on compressed-reconstructed signals
  3. Train and test on compressed-reconstructed signals

## Datasets

- **Primary dataset:** MIT-BIH Arrhythmia Database
- **Validation dataset:** INCART 12-lead Arrhythmia Database (limited validation)

## Methodological Focus

This project emphasizes:
- realistic sequential compression-to-classification evaluation,
- inter-patient train/test separation,
- standardized class mapping,
- unified compression accounting across methods,
- and cross-comparable evaluation between traditional and deep learning pipelines.

## Public Release Note

This public repository is intended for academic visibility and project presentation.

It currently contains the project overview, methodology summary, and selected results from an ongoing study. The complete experimental pipeline is not included in this public version while the associated manuscript is in preparation.

## Project Status

- Literature review completed
- Core benchmark design implemented
- Primary experiments in progress / completed
- Manuscript in preparation for journal submission

## Planned Repository Contents

This repository will gradually include:
- project overview documents,
- selected figures and result summaries,
- a results-oriented notebook,
- and additional research materials suitable for public release.

## Copyright Notice

Copyright © 2026 Monzurul Islam. All rights reserved.

This repository is shared for academic visibility and discussion purposes only. Reuse, redistribution, or derivative publication of the project materials without explicit permission is not allowed.
