# data-create-camp-2024

## Fashion Style Image Classification & Preference Prediction


## Preliminary

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