# Meta-learning Integration of Unsupervised Anomaly Scores for Intrusion Detection in Defense Network Environments

> Unsupervised anomaly detection framework for defense network intrusion detection using heterogeneous anomaly scores and meta-learning.

## Overview

This repository contains the implementation of the paper:

**Meta-learning Integration of Unsupervised Anomaly Scores for Intrusion Detection in Defense Network Environments**

The goal of this study is to improve intrusion detection performance in defense network environments where labeled attack data are limited and anomaly patterns continuously evolve.  
To address this problem, we first generate anomaly scores from multiple unsupervised anomaly detection models and then integrate them through a neural-network-based meta-learning framework. The proposed approach is designed to reduce threshold sensitivity and improve robust detection performance across different network datasets.  [oai_citation:1‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

---

## Key Features

- Uses **five unsupervised anomaly detection models**
  - k-Nearest Neighbors (kNN)
  - Local Outlier Factor (LOF)
  - Isolation Forest (IF)
  - Histogram-based Outlier Score (HBOS)
  - Autoencoder (AE)
- Compares two preprocessing strategies
  - **Min-Max normalization**
  - **Quantile transformation**
- Applies **bootstrap-based threshold estimation**
- Integrates heterogeneous anomaly scores with a **meta-learning neural network**
- Evaluated on two benchmark intrusion detection datasets
  - **NSL-KDD**
  - **UNSW-NB15**  [oai_citation:2‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

---

## Motivation

Traditional signature-based intrusion detection systems are effective for known attack patterns, but they are limited in dynamic environments such as defense networks, where new or evolving attacks may not match predefined signatures.  
Unsupervised anomaly detection is a practical alternative because it can learn the structure of normal traffic without requiring labeled attack data. However, its performance often depends heavily on preprocessing choices, anomaly score distributions, and threshold selection.

This repository implements a framework that combines multiple anomaly scores through meta-learning to overcome the limitations of individual unsupervised models.  [oai_citation:3‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

---

## Methodology

### 1. Preprocessing
Two preprocessing methods are considered for numerical variables:

- **Min-Max normalization**
- **Quantile transformation**

These methods were compared to examine how they affect anomaly score distributions and downstream detection performance.  [oai_citation:4‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

### 2. Base Unsupervised Models
Five unsupervised anomaly detection models are trained:

- **kNN**
- **LOF**
- **Isolation Forest**
- **HBOS**
- **Autoencoder**  [oai_citation:5‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

### 3. Bootstrap-based Thresholding
For each model, thresholds are estimated using bootstrap resampling on anomaly scores computed from normal validation data.  
Specifically, 500 bootstrap samples are generated, and the threshold is determined from the median of the resulting threshold distribution.  [oai_citation:6‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

### 4. Meta-learning
The anomaly scores from the selected unsupervised models are used as input features to a meta-model.  
The meta-model is a neural network with:

- 5-dimensional input
- 1 hidden layer
- Sigmoid output for binary classification
- Binary cross-entropy loss
- Adam optimizer  [oai_citation:7‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

---

## Datasets

### NSL-KDD
- Designed to improve the structural issues of KDD Cup 99
- 41 explanatory variables and 1 response variable
- Train set: **125,973**
- Test set: **22,544**  [oai_citation:8‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

### UNSW-NB15
- Designed to better reflect modern network environments and attack scenarios
- 42 explanatory variables and 2 response variables
- Train set: **175,341**
- Test set: **82,332**  [oai_citation:9‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

---

## Hyperparameter Settings

| Model | Hyperparameters |
|---|---|
| kNN | Gower distance, k = 1 |
| LOF | Gower distance, k = 10 |
| IF | 100 trees, subsampling size = 512, feature subsampling ratio = 0.4 |
| HBOS | Number of bins = 10 |
| AE | Hidden layers = 32, 24; latent dimension = 16; Adam; learning rate = 0.001; batch size = 128; epochs = 500; early stopping = 20; tanh activation |  [oai_citation:10‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

---

## Main Results

### Model-wise AUC Comparison
On **NSL-KDD**, most models achieved strong discrimination performance, and **AE** and **IF** showed particularly high AUC values.  
On **UNSW-NB15**, model performance varied more substantially depending on preprocessing and algorithmic characteristics.  [oai_citation:11‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

### Meta-model Performance
The meta-learning framework achieved strong overall performance on both datasets:

| Dataset | AUC | Precision | Recall | Specificity | F1 Score |
|---|---:|---:|---:|---:|---:|
| NSL-KDD | 0.97 | 0.92 | 0.90 | 0.89 | 0.91 |
| UNSW-NB15 | 0.95 | - | 0.97 | 0.62 | 0.85 |

The results suggest that integrating heterogeneous anomaly scores improves robustness and reduces sensitivity to individual threshold settings.  [oai_citation:12‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)  [oai_citation:13‡3.18 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지.pdf](sediment://file_000000002b4072068d85358c92e9e196)

---

## Repository Structure

```bash
.
├── data/
│   ├── raw/
│   ├── processed/
│   └── README.md
├── preprocessing/
│   ├── minmax.py
│   ├── quantile.py
│   └── encoding.py
├── models/
│   ├── knn.py
│   ├── lof.py
│   ├── iforest.py
│   ├── hbos.py
│   └── autoencoder.py
├── thresholding/
│   └── bootstrap_threshold.py
├── meta_learning/
│   └── meta_model.py
├── experiments/
│   ├── run_nsl_kdd.py
│   └── run_unsw_nb15.py
├── results/
│   ├── figures/
│   ├── tables/
│   └── logs/
├── requirements.txt
└── README.md