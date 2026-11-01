# 국방 네트워크 환경에서의 비지도 이상점수 통합 메타학습 기반 이상탐지

> 비지도 이상탐지 모델 5개(kNN, LOF, HBOS, IF, AE)가 산출한 **이상점수를 신경망 메타모델로 통합**해 네트워크 침입을 탐지합니다.
> **결론: 메타모델이 두 데이터셋 모두에서 개별 모델보다 높은 AUC를 기록했습니다 (NSL-KDD 0.98, UNSW-NB15 0.95).**

📄 논문: [https://doi.org/10.23199/jdqs.2026.8.1.001](https://doi.org/10.23199/jdqs.2026.8.1.001)

## 핵심 결과

**AUC 비교** (Quantile Transformation 전처리 기준)

| Dataset | kNN | LOF | HBOS | IF | AE | **Meta-model** |
|---|---|---|---|---|---|---|
| NSL-KDD | 0.96 | 0.83 | 0.95 | 0.97 | 0.97 | **0.98** |
| UNSW-NB15 | 0.87 | 0.91 | 0.75 | 0.82 | 0.90 | **0.95** |

**메타모델 성능**

| Dataset | AUC | Precision | Recall | Specificity | F1 |
|---|---|---|---|---|---|
| NSL-KDD | 0.98 | 0.96 | 0.89 | 0.95 | 0.93 |
| UNSW-NB15 | 0.95 | 0.77 | 0.97 | 0.65 | 0.86 |

- 개별 모델의 순위가 데이터셋마다 다릅니다. NSL-KDD에서는 IF가 강하고 LOF가 약하지만, UNSW-NB15에서는 반대입니다.
- 메타모델은 **어느 한 모델에 의존하지 않고** 두 데이터셋 모두에서 가장 좋은 AUC를 냈습니다.
- UNSW-NB15에서는 재현율이 0.97로 높은 대신 특이도가 0.65로 낮습니다. 공격은 거의 놓치지 않지만 정상 트래픽 일부를 공격으로 판정한다는 뜻입니다.

## 연구 배경

시그니처 기반 침입탐지는 라벨과 사전 정의된 공격 패턴에 의존하기 때문에, 새로운 공격이 계속 등장하는 국방 네트워크 환경에 대응하기 어렵습니다. 그래서 이 연구는 **라벨 없이 학습하는 비지도 이상탐지**를 기반으로 하고, 모델마다 다른 탐지 특성을 **메타학습으로 통합**해 강건성을 높였습니다.

## 방법

```
정상 트래픽 (train 80% / valid 20%)
        │  전처리: Min-Max vs Quantile Transformation
        ▼
┌──────┬──────┬──────┬──────┬──────┐
│ kNN  │ LOF  │ HBOS │  IF  │  AE  │   개별 비지도 모델 → 이상점수
└──────┴──────┴──────┴──────┴──────┘
        │  붓스트랩 기반 임계값 (valid 점수 분포)
        ▼
  5차원 이상점수 벡터 ──▶ 신경망 메타모델 ──▶ 공격 확률
```

- **전처리 비교**: 네트워크 트래픽은 왜도가 크고 꼬리가 두껍습니다. 그래서 Min-Max 정규화와 Quantile Transformation을 비교했고, 대부분의 모델에서 Quantile이 우세했습니다.
- **붓스트랩 임계값**: 검증 데이터의 이상점수를 재표본해 임계값을 추정합니다. 이렇게 하면 단일 분위수로 임계값을 정할 때의 불안정성이 줄어듭니다.
- **메타모델**: 5개 모델의 이상점수를 입력으로 받아 공격 확률을 출력하는 인공신경망입니다.
- **데이터셋**: NSL-KDD(`KDDTrain+` / `KDDTest+`)와 UNSW-NB15

## 재현

이 결과는 **Apple M1 MacBook Air(CPU)** 에서 재현을 확인했습니다. 수치를 그대로 재현하려면 노트북 코드와 시드(`seed=42`)를 바꾸지 말고, 고정된 패키지 버전을 사용하세요.

```bash
conda create -n ia3-clean python=3.10.16
conda activate ia3-clean
pip install -r requirements.txt
```

| 단계 | 실행 위치 | 노트북 | 입력 → 출력 |
|---|---|---|---|
| 1. 개별 모델 | `anomaly_detection_model/` | `kNN`, `LOF`, `HBOS`, `IF`, `AE.ipynb` | `data/*.csv` → 전처리·α별 성능표 |
| 2. 메타모델 | `meta_model/` | `meta_model.ipynb` | `*_meta_Quantile/*.csv` → 메타모델 성능표 |

- 각 노트북은 **해당 폴더를 작업 디렉터리로** 두고 실행해야 합니다. 데이터는 상대경로로 읽습니다.
- `meta_model/*_meta_Quantile/`에는 1단계 모델들이 산출한 이상점수가 CSV로 미리 저장돼 있습니다. 그래서 2단계는 1단계를 다시 돌리지 않아도 바로 실행할 수 있습니다.
- 노트북마다 실행 결과가 저장돼 있어서, 실행하지 않고도 수치를 확인할 수 있습니다.

```
├── anomaly_detection_model/
│   ├── data/                  # 전처리·분할된 NSL-KDD, UNSW-NB15 (MinMax / Quantile)
│   └── {kNN,LOF,HBOS,IF,AE}.ipynb
├── meta_model/
│   ├── {모델}_meta_Quantile/  # 개별 모델 이상점수 (train80 / abnormal / test)
│   └── meta_model.ipynb
└── requirements.txt           # 재현 환경 버전 고정
```
