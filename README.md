# data-create-camp-2024

## Fashion Style Image Classification & Preference Prediction


## Preliminary Round

### MISSION1 : 패션 스타일 이미지 분류
**ResNet-18 모델을 통해 "성별 & 스타일" 단위로 클래스 분류를 수행하고 정학도를 제시한다.** 


## MISSION 1: 패션 스타일 이미지 분류 (Style Classification)

### 목표
- ResNet-18 모델을 활용해 이미지의 **성별 & 스타일**을 분류
- 클래스별 정확도(Accuracy)를 측정하고 성능을 향상시키기 위한 전처리 및 데이터 증강 전략 설계

### 전처리 및 데이터 증강 (Data Augmentation)
클래스 간 데이터 불균형 문제를 해결하고, 모델의 일반화 성능을 향상시키기 위해 다음과 같은 기법을 적용.

| 기법 | 설명 | 목적 |
|------|------|------|
| Resize + CenterCrop | 이미지 중심 부분을 자름 | 중심부에 집중된 주요 스타일 정보를 효과적으로 학습 |
| RandomHorizontalFlip | 좌우 반전 | 방향성에 강인한 인식 능력 부여 |
| RandomRotation | 임의의 각도 회전 | 다양한 시점에서도 일관된 인식 가능 |
| RandomAffine | 스케일, 이동, 기울기 등 변환 | 실제 사용 환경에서의 왜곡/변형에 강인한 특징 추출 가능 |

---

## MISSION 3: 패션 스타일 선호 여부 예측 (Preference Prediction)

### 목표
- Item-based Filtering 기반 추천 시스템 구현
- 사용자가 특정 스타일을 "선호하는지 여부"를 이미지 간 유사도를 활용해 예측

### 전체 프로세스
1. **Feature Extraction**  
   - 학습된 ResNet-18 모델의 중간 feature vector를 활용해 이미지 임베딩 추출

2. **유사도 기반 예측**  
   - Validation 데이터의 각 사용자에 대해:
     - 선호 이미지, 비선호 이미지 각각의 feature vector 저장
     - 이미지 간 cosine similarity 계산
     - 선호 이미지와 Validation 이미지 간 유사도가 높은 경우 → 선호로 예측

3. **Thresholding 기반 이진 분류**  
   - cosine similarity ≥ threshold → 선호(True)로 예측  
   - cosine similarity < threshold → 비선호(False)로 예측

---

### 전략적 접근: 사용자 맞춤형 Threshold 최적화
- 모든 사용자에게 동일한 임계값을 적용하지 않고, **사용자별 최적 threshold**를 찾아 personalized recommendation을 강화

### 최적화 방법: F1-Score 기반 Threshold Tuning
- F1 Score는 Precision과 Recall의 조화평균으로, 과도한 추천과 과소 추천의 균형을 측정할 수 있는 지표
- 각 사용자마다 threshold 후보값을 0.01 ~ 1.00 (step=0.01)으로 설정하고, 모든 threshold에 대해 F1 Score를 측정
- 가장 높은 F1 Score를 나타내는 threshold를 해당 사용자의 개인 최적 threshold로 설정

---

### 결과 기대 효과
- 사용자별 선호 경향 반영 → 추천 정확도 개선
- overly optimistic / conservative recommendation 방지
- 비지도 기반 개인화 추천 시스템의 Threshold tuning 자동화 시도

---

## Final Round

## MISSION: Ranker - 이미지 선호도 예측 정확도 향상

### Goal
- 단순한 이미지 간 유사도 기반 예측을 넘어, **유저의 설문 정보(survey data)**를 활용하여  
  이미지 선호 예측의 정확도를 더욱 향상시키는 것이 목표.

---

### 접근 방법

#### 1. Feature 구성

- **Image Feature Vector**  
  - Mission 3-2에서 ResNet-18을 활용하여 추출한 이미지 임베딩

- **Survey Feature Vector**  
  - 설문 데이터를 다음 세 가지 방법 중 하나로 임베딩  
    - FCN (Fully Connected Network)  
    - AutoEncoder  
    - BERT (Transformer 기반)

#### 2. Feature 결합 및 유사도 계산

- `image feature`와 `survey feature`를 **concatenate**한 후,  
  cosine similarity를 기반으로 유사도 계산 및 예측 수행

---

### 실험 결과 및 한계 분석

- 단순히 survey feature를 concat하여 사용했을 때,  
  일부 모델(FCN, AutoEncoder)의 경우 **기존 이미지 feature만 사용한 경우와 유사한 성능** 수준에 머무름

- 따라서 **기존 방식으로는 성능 향상이 제한적**이라는 결론에 도달  
  → **새로운 방식 제안이 필요**

---

### 성능 향상 전략: BERT 기반 Survey Feature 정제 및 정규화

- BERT로 추출한 survey feature vector와 image feature vector를 concat한 후,  
  **정규화(normalization)**를 적용해 **유사도 계산에 사용되는 스케일 문제를 완화**

- 이 접근을 통해 **가장 높은 유사도 기반 정확도**를 달성

---

### 최종 성과

- **Max Similarity Accuracy: 88.28%**  
  → 기존 image-only 기반 방식 대비 정확도 향상 확인  
  → BERT + Normalization 기반 survey feature 활용이 효과적임을 실험적으로 입증