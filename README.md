 ECG Compression–Classification Benchmark

Benchmarking how ECG compression affects downstream arrhythmia classification in realistic telemedicine workflows.

---

## Project Overview

This repository presents an ongoing research project investigating how different ECG compression techniques affect downstream arrhythmia classification performance in a realistic end-to-end telemedicine workflow.

ECG signal compression is widely used to reduce storage and transmission costs in wearable devices and telemedicine systems. However, most ECG compression studies focus on improving compression metrics (CR, PRD, WEDD) without examining how compressed-reconstructed signals affect downstream classification models. Similarly, most ECG classification studies evaluate on raw signals only. In practice, however, compression and classification are used together.

This project is designed to address that gap by evaluating ECG compression and downstream classification together in a realistic telemedicine-oriented setting.

## Benchmark Design

**Compression methods (4)**

| Family | Description |
|---|---|
| Wavelet | DWT + scalar quantisation + Huffman entropy coding |
| Hybrid | DWT + dead-zone quantisation + run-length encoding + zlib |
| Deep CAE | 1-D convolutional autoencoder (trained on DS1, evaluated on DS2) |
| Near-lossless | Single low-distortion operating point |

**Distortion levels (3 for lossy methods)**

| Tag | Target PRD | Use case |
|---|---|---|
| MILD | 4–5% | Routine archival, conservative telemedicine |
| MOD  | 10–12% | Bandwidth-constrained transmission |
| AGGR | 20–25% | Stress test, edge / wearable scenarios |

**Downstream classifiers (2)**

- **Traditional** — Random Forest with RR-interval and morphological features
- **Deep learning** — 1-D CNN trained on standardised beat windows

**Evaluation scenarios (3)**

| ID | Train on | Test on | What it measures |
|---|---|---|---|
| **S1** | Raw | Raw | Upper-bound baseline |
| **S2** | Raw | Reconstructed | Deploying an existing model on compressed data |
| **S3** | Reconstructed | Reconstructed | Retraining to match the deployment domain |

**Dataset.** MIT-BIH Arrhythmia Database with the standard inter-patient DS1/DS2 split (De Chazal et al.) and AAMI 3-class mapping (N, S, V).

## Selected Results

> **Headline finding:** The choice of *classifier architecture* matters more than the choice of *compression codec*. A traditional RR-feature classifier loses performance sharply under compression and cannot recover it via retraining, while a deep CNN classifier absorbs compression with little loss and in some configurations actually *exceeds* its raw-signal baseline.

### Figure 1 — Classification quality vs compression distortion

![Macro-F1 vs distortion for both classifiers and scenarios](docs/docs/fig1_macro_f1_vs_distortion.png)

**Top row (Traditional classifier):** All codecs hug the raw baseline in S2, then lose 7–18 macro-F1 points in S3 as distortion increases. Retraining on reconstructed signals does not help.

**Bottom row (Deep learning classifier):** Much flatter degradation curves. In the bottom-right panel (DL, S3), the Deep CAE line sits *above* the raw baseline at mild and moderate distortion — a CNN trained on Deep-CAE-reconstructed ECG outperforms one trained on raw ECG (macro-F1 = 0.876 vs baseline 0.821).

### Figure 2 — Rare-class (S) performance under retraining (S3)

![S-class F1 for traditional vs deep learning](docs/docs/fig2_s_class_f1.png)

**Left panel (Traditional):** Supraventricular (S) class F1 collapses from a baseline of 0.59 to roughly 0.17–0.23 at aggressive distortion. The classifier cannot recover the rare class even when retrained on the deployment domain.

**Right panel (Deep learning):** The CNN retains much higher S-class F1 across all distortion levels. At mild distortion with Deep CAE, the CNN reaches S-class F1 = 0.752, well above its own raw baseline of 0.650. The smoother reconstructions may act as an implicit regulariser, suppressing high-frequency noise that the convolutional features over-fit to on raw input.

### Figure 3 — Compression ratio vs end-to-end macro-F1

![CR vs end-to-end macro-F1 trade-off](docs/docs/fig3_compression_classification_tradeoff.png)

Both classifiers follow the expected trend — quality drops as compression ratio increases — but the slope is much gentler for the CNN. Several DL data points sit above the baseline, while the traditional pipeline never crosses its baseline. A system designer choosing a codec for a CNN-based classifier has substantially more headroom and can pick a much higher compression ratio for the same downstream quality.

### Figure 4 — End-to-end accuracy across all compression conditions

![End-to-end accuracy bar chart](docs/docs/fig4_e2e_accuracy.png)

This bar chart shows the **end-to-end accuracy** (defined as `accuracy × coverage`, i.e. the fraction of all gold-truth beats that the full pipeline both detects and classifies correctly) for every codec × distortion combination, separately for the two classifiers. Solid bars are S2, hatched bars are S3.

Two things are immediately visible:

1. **End-to-end accuracy collapses from ~0.96 (raw baseline) to ~0.57–0.74 once compression is introduced**, regardless of codec or classifier.
2. **The two classifier panels look almost identical**, and S2 and S3 bars within each panel are also nearly identical.

This is the *honest* deployment picture and it complements — rather than contradicts — Figures 1 and 2. The reason both panels look the same is that end-to-end accuracy is **dominated by the R-peak detection step**, not by the classifier. When the QRS detector misses 24–40% of the beats on a reconstructed signal, those misses become errors in the end-to-end metric, and they swamp any difference the classifier choice might make on the beats that *are* detected.

**The system-design implication is sharper than any single figure.** On per-detected-beat metrics (Figures 1, 2) the CNN clearly outperforms the traditional classifier under compression. On the end-to-end metric, both classifiers converge to the same coverage-bound ceiling. The bottleneck for a real telemedicine pipeline is therefore not the classifier — it is the QRS detector running on reconstructed signals. Any future work that wants to improve end-to-end accuracy under compression must improve detection robustness, not just classification robustness.

### Key Findings

1. **Classifier architecture matters more than codec choice** *for per-detected-beat metrics*. At any fixed (codec, distortion) combination, switching from the traditional to the CNN pipeline buys back more performance than any codec change.
2. **Deep learning sometimes benefits from compressed signals.** A CNN trained on Deep-CAE-reconstructed ECG at mild distortion yields macro-F1 = 0.876 and S-class F1 = 0.752, both above the raw-trained baseline. This is consistent across the lossless and hybrid codecs at mild distortion as well.
3. **The traditional pipeline collapses on the rare class.** Hand-crafted RR and morphological features cannot survive once compression erases the discriminative morphology.
4. **Mild compression is essentially free for per-detected-beat metrics.** At PRD ≈ 5%, every codec costs less than ~2 macro-F1 points for both classifiers.
5. **For end-to-end accuracy, the QRS detector — not the classifier — is the bottleneck.** Once R-peak detection runs on reconstructed signals, both classifiers converge to the same coverage-bound ceiling (~0.57–0.74 end-to-end accuracy depending on distortion). Improving end-to-end performance under compression requires improving detection robustness, not just classification robustness.

## Methodology

A rigorous experimental framework is used to ensure valid comparisons between compression modalities and classification architectures:

- **Sequential processing:** compression and classification are evaluated as separate stages in a realistic transmission–storage–diagnosis workflow.
- **Data leakage prevention:** inter-patient train-test separation is maintained in both the compression and classification experiments.
- **Standardized categorization:** a three-class AAMI-compliant mapping (N, S, V) is used for arrhythmia classification tasks.
- **Unified compression accounting:** common definitions of CR and PRD are enforced across all four compression techniques to maintain fair benchmarking and cross-comparability.
- **Record-wise evaluation:** CR and PRD are computed over the entire standardised ECG record rather than beat-by-beat, to make distortion measures comparable across methods.

## Evaluation Metrics

**Compression:** CR, PRD, WEDD

**Classification:** Macro-F1, End-to-end Macro-F1, Macro-AUC, Macro-AUPRC (One-vs-Rest), F1-score for Supraventricular (S) beats, Class-specific ROC-AUC for N, S, and V classes, Coverage (fraction of gold beats detected post-reconstruction)

## Public Release Note

This public repository is intended for academic visibility and project presentation. It contains the project overview, methodology summary, selected results, and aggregated benchmark data from an ongoing study. The complete experimental pipeline is not included in this public version because the associated manuscript is still in preparation.

## Project Status

- [x] Literature review completed
- [x] Core benchmark design implemented
- [x] Primary experiments completed / in refinement
- [x] Selected results organised for presentation
- [ ] Manuscript in preparation for journal submission
- [ ] Validation on INCART database (planned)

## Copyright Notice

Copyright © 2026 Md Monzurul Islam Rabu. All rights reserved.

This repository is shared for academic visibility and discussion purposes only. Reuse, redistribution, or derivative publication of the project materials without explicit permission is not allowed.
