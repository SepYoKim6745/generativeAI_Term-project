# CycleGAN을 활용한 날씨 변환 프로젝트

## 설명
- 이 프로젝트는 CycleGAN을 사용하여 맑은 날씨 이미지를 비 오는 날씨 이미지로 변환하는 모델을 학습합니다.
- 이 과정은 자율주행 차량의 다양한 날씨 조건 대응 능력을 강화하는데 활용됩니다.
- 이 프로젝트의 소스코드는 CycleGAN GitHub를 통해 만들어지고 학습되었습니다.
---

## 데이터셋

> 데이터셋 용량이 커서 GitHub에 포함되지 않습니다.
대신, Google Drive에서 다운로드하여 로컬 환경에 설정해야합니다.

### 데이터 출처
- 데이터셋은 AIHUB의 **승용 자율주행차 악천후 데이터**를 가공하여 사용하였습니다.
- 자세한 정보는 [AIHUB 데이터셋 페이지](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=data&dataSetSn=71626)를 참고하세요.

### 다운로드 및 설정 방법

1. [Google Drive 링크](https://drive.google.com/your_dataset_link)를 통해 데이터셋을 다운로드합니다.
2. 다운로드 후, 아래와 같은 디렉터리 구조로 데이터를 정리합니다

### 데이터셋 디렉터리 구조
데이터셋은 아래와 같은 구조로 정리되었습니다:

``` shell
./datasets/
├── testA # 맑은 날씨 이미지 (테스트용) 
├── testB # 비 오는 날씨 이미지 (테스트용) 
├── trainA # 맑은 날씨 이미지 (학습용) 
└── trainB # 비 오는 날씨 이미지 (학습용) 
```

### 데이터 구성
- **Train**:
  - 맑음: 4,400장
  - 비: 4,400장
  - 총합: 8,800장
- **Test**:
  - 맑음: 100장
  - 비: 100장
  - 총합: 100장


### 데이터 전처리
- 원본 이미지 크기: `1920×1080` -> `512x512`
- 도메인 A: 맑은 날씨 이미지
- 도메인 B: 비 오는 날씨 이미지



---

## 학습된 모델 다운로드
학습된 모델은 [Google Drive 링크](https://drive.google.com/drive/folders/1oPsO7psLeR8V-vOlpQTFKrb6WcKNSvM2?usp=drive_link)에서 다운로드할 수 있습니다. 다운로드 후, `checkpoints/` 폴더에 압축을 해제하세요.

## 학습 방법
CycleGAN 학습에 사용한 명령어는 다음과 같습니다:
```shell
python train.py \
  --dataroot ./datasets \
  --name weather \
  --model cycle_gan \
  --batch_size 1 \
  --load_size 512 \
  --crop_size 512
```

### 명령어 옵션 설명:
- --dataroot: 데이터셋 경로.
- --name: 학습 세션 이름 (결과가 저장될 디렉터리 이름).
- --model: CycleGAN 모델 사용.
- --batch_size: 배치 크기 (1장씩 학습).
- --load_size: 학습 전 이미지 크기 조정.
- --crop_size: 학습에 사용될 최종 이미지 크기.

---
## 결과물
- 학습 결과는 results/weather 디렉터리에 저장됩니다.
- 생성된 이미지는 두 가지 변환 결과를 포함합니다:
    - 맑은 날씨 → 비 오는 날씨
    - 비 오는 날씨 → 맑은 날씨

### 결과물 샘플
아래는 CycleGAN 학습 결과로 생성된 이미지 샘플입니다:
- 1차 평가<br>

| 맑은 날씨 (원본) | 비 오는 날씨 (변환) |
|------------------|--------------------|
| ![Clear](results/sample_clear1.jpg) | ![Rainy](results/sample_rainy1.jpg) |

- 2차 평가(예정) <br>



### 평가 방법
본 텀프로젝트에서 CycleGAN을 평가하는 방법은 손실 값 분석, 시각적 평가, 수치적 평가를 통해 종합적으로 평가합니다.

1. 손실 값 분석<br>
- Cycle Consistency 손실 (Cycle_A Loss, Cycle_B Loss):
    - 변환 과정에서 원본 정보를 얼마나 유지하는지를 평가.
    - 값이 낮고 안정적으로 수렴해야 합니다.
- Generator와 Discriminator 손실의 균형:
    - 생성기와 판별기의 손실 값이 유사한 수준에서 안정적으로 수렴하는 것이 이상적입니다.

2. 시각적 평가<br>
학습 중 저장된 결과 이미지를 확인하여 모델이 변환을 얼마나 잘 수행했는지 평가합니다.
- 이미지 비교:
    - real_A(원본 도메인 A) ↔ fake_B(변환된 도메인 B).
    - real_B(원본 도메인 B) ↔ fake_A(변환된 도메인 A).
- 변환된 이미지가 원본 이미지의 핵심 특징을 유지하면서 타겟 도메인의 스타일을 반영하고 있는지 확인.

3. 수치적 평가<br>
CycleGAN의 성능을 정량적으로 평가하기 위해 다음 지표를 사용할 수 있습니다:

- SSIM (Structural Similarity Index):
    - 변환된 이미지가 원본 이미지와 구조적으로 얼마나 유사한지 평가.
    - 값 범위: 0 ~ 1 (1에 가까울수록 원본과 유사).
- PSNR (Peak Signal-to-Noise Ratio):
    - 변환된 이미지가 원본 이미지와 얼마나 가까운지 평가하는 신호 대 잡음비.
    - 값이 높을수록 더 유사.

---

## 참고자료
- [CycleGAN 논문](https://arxiv.org/abs/1703.10593)
- [AIHUB 데이터셋 페이지](https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=data&dataSetSn=71626)
- [CycleGAN GitHub 코드베이스](https://github.com/junyanz/pytorch-CycleGAN-and-pix2pix)