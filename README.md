# Unsupervised Network Intrusion Detection: Performance Comparison with Bootstrap-based Thresholding
> 비지도 학습기반 사이버보안 네트워크 이상 탐지 성능 비교 분석  
> Datasets: NSL-KDD, UNSW-NB15 | Models: LOF, kNN, HBOS, Isolation Forest, AutoEncoder

## 1. Overview
본 저장소는 네트워크 침입 탐지(Network Intrusion Detection) 문제에서 **비지도 이상탐지 모델(LOF, kNN, HBOS, IF, AE)**의 성능을 비교 분석하기 위한 연구 코드/결과를 포함합니다.  
핵심 아이디어는 각 모델이 산출한 이상 점수(anomaly score)에 대해 **부트스트랩(bootstrap)을 이용해 임계값(threshold)의 변동성을 안정화**한 뒤, 최종 임계값을 **α 분위수(예: 0.85, 0.90)** 기반으로 결정하는 것입니다.

- **전처리 비교**: Min-Max Scaling vs Quantile Transform  
- **임계값 결정**: 정상(normal) 점수 분포에서 bootstrap으로 분위수 기반 임계값 후보를 생성 → 후보들의 분포에서 대표값(중앙값 또는 분위수 기준)을 최종 threshold로 사용  
- **목표**: (모델 × 전처리 × α 분위수) 조합별 탐지 성능을 공정하게 비교하여 최적 조합을 제시

## 2. Repository Structure

> **Note**: 데이터셋의 재배포가 제한되는 경우, `data/`는 비워두고 다운로드 방법만 안내합니다.

## 3. Datasets
- **NSL-KDD**: 네트워크 침입 탐지 벤치마크 데이터셋
- **UNSW-NB15**: 현대 공격 유형을 포함한 침입 탐지 데이터셋

### Data availability
본 저장소는 저작권/라이선스 준수를 위해 원본 데이터는 포함하지 않을 수 있습니다.  
필요한 경우 아래 절차로 데이터를 준비하세요.

1) NSL-KDD 다운로드: [링크/출처 기입]  
2) UNSW-NB15 다운로드: [링크/출처 기입]  
3) 다운로드 후 `data/raw/`에 배치  
4) 전처리 스크립트 실행(아래 참고)

## 4. Methods
### 4.1 Preprocessing
- **Min-Max Scaling**
- **Quantile Transform** (분포 기반 변환)

### 4.2 Models (Unsupervised)
- **LOF (Local Outlier Factor)**
- **kNN distance-based outlier**
- **HBOS (Histogram-based Outlier Score)**
- **Isolation Forest**
- **AutoEncoder (AE)**

### 4.3 Bootstrap-based Thresholding
정상 데이터의 이상 점수 분포에서 bootstrap 샘플을 반복 추출하고, 각 반복에서 **α 분위수 임계값**을 계산합니다.  
이렇게 얻은 임계값 후보들의 분포를 요약하여(예: 중앙값/대표 분위수) 최종 threshold를 결정합니다.

- α ∈ {0.85, 0.90} (실험 설정)
- 모든 모델에 동일한 α를 적용하여 **비교의 공정성**을 확보

## 5. Evaluation
- 주요 지표: Accuracy / Precision / Recall(Detection Rate) / F1 / FAR(False Alarm Rate) / AUC(가능 시)
- 데이터셋(NSL-KDD, UNSW-NB15) 각각에 대해
  - 전처리 2종 × 모델 5종 × α 2종 조합 비교

## 6. Reproducibility
### 6.1 Environment
- Python >= 3.10 (권장)
- (예) requirements: `requirements.txt` 또는 `environment.yml`

```bash
pip install -r requirements.txt