# ✅ README.md 

```md
# 🚀 AI Challenge — VQA (Visual Question Answering)
## E026_NULL 먹던걸로(김혜령(팀장), 박정원, 정승현, 진채영)

> 본 레포지토리는 대회 제공 **베이스라인코드(`251023_Baseline.ipynb`)**을 기반으로  
> 팀이 직접 개선하여 성능을 높인 **베스트 코드(`E026_NULL 먹던걸로.ipynb`)**를 포함합니다.  
> 이 노트북 하나로 **학습 → 추론 → 제출 파일(submission.csv)** 생성까지 가능합니다.

---

## 📌 1. Project Overview

| 항목 | 내용 |
|---|---|
| 대회명 | [대회 정확한 이름 작성] |
| 문제 유형 | **MCQ 기반 VQA (이미지 + 질문 + 보기 선택)** |
| 목표 | 시각 정보 + 텍스트를 함께 이해하는 모델 학습 및 정확도 향상 |
| 산출물 | `/content/submission.csv` |

---

## 📁 2. Repository Structure

```

📦 project
└─ base_code/
├─ 251023_Baseline.ipynb          # 대회 제공 초기 코드 (원본 보존)
└─ E026_NULL 먹던걸로.ipynb        # ✅ BEST 코드 (학습 + 추론 + 제출)
└─ README.md

```

---

## 🛠 3. Environment

> Colab 기준 실행 가능하도록 작성됨

```

transformers >= 4.44.2
torch
pandas
Pillow
tqdm
peft         # LoRA fine-tuning 가능

````

### 설치 코드 (노트북 첫 셀에 포함됨)
```bash
!pip -q install "transformers>=4.44.2" "accelerate" "peft"
````

---

## 🗂 4. Dataset

데이터는 CSV 기준으로 아래 컬럼을 사용합니다.

| column           | 설명                     |
| ---------------- | ---------------------- |
| `image` / `path` | 이미지 경로                 |
| `question`       | 질문                     |
| `a, b, c, d`     | Multiple-choice 선택지    |
| `answer`         | 정답(label, train에서만 사용) |

> **경로는 `/content/` 기준** (Colab 환경 고려)

---

## 🚀 5. How to Run

1. `base_code/E026_NULL 먹던걸로.ipynb` 열기
2. 상단의 `pip install` 셀 실행
3. `train.csv`, `test.csv`, 이미지 업로드
4. **학습 셀 실행 → 추론 셀 실행 → 제출파일 생성**

* 결과 파일:

  ```
  /content/submission.csv
  ```

---

## 🔧 6. Baseline → Best 변경 사항

### ✅ 변경 요약표

| 변경 항목           | Baseline                 | Our Best (최종 제출)         | 기대 효과                               |
| --------------- | ------------------------ | ------------------------ | ----------------------------------- |
| **Model**       | `Qwen2.5-VL-3B-Instruct` | ✅ `Qwen3-VL-4B-Instruct` | **이해력 & 정확도 향상 / 수렴 안정화**           |
| **IMAGE_SIZE**  | 384                      | ✅ 448                    | **작은 객체·텍스트 등 세밀한 시각 정보 인식**        |
| **학습 데이터 수**    | `sample(n=200)`          | ✅ `sample(n=400)`        | **일반화 & loss 안정화**                  |
| **LoRA 설정**     | `r=8`, `alpha=16`        | ✅ `r=16`, `alpha=64`     | **표현력 증가 (문맥/이미지 연결 강화)**           |
| **Fine-Tuning** | 없음                       | ✅ `EPOCHS=2`, `LR=2e-4`  | **빠른 수렴 + 안정적 학습**                  |
| **Prompt 언어**   | 한글 prompt                | ✅ 영어 prompt              | **LLM 사전학습 분포 상 영어가 더 유리 → 정확도 상승** |

---

## 🧠 7. 변경 코드 (핵심 스니펫)

```python
# ✅ Model 변경
MODEL_NAME = "Qwen/Qwen3-VL-4B-Instruct"

# ✅ Image Size 변경
IMAGE_SIZE = 448

# ✅ 학습 데이터 수 증가
train_df = train_df.sample(n=400, random_state=42)

# ✅ LoRA 변경
lora_config = LoraConfig(
    r=16,           # 기존: 8
    lora_alpha=64,  # 기존: 16
    lora_dropout=0.05,
    bias="none",
)

# ✅ Fine-tuning parameter
EPOCHS = 2
LR = 2e-4

# ✅ Prompt 변경 (영어)
def build_mc_prompt(question, a, b, c, d):
    return (
        "You are a helpful VQA assistant.\n"
        "Select one option from [a, b, c, d].\n\n"
        f"Question: {question}\n"
        f"a) {a}\n"
        f"b) {b}\n"
        f"c) {c}\n"
        f"d) {d}\n"
        "Answer:"
    )
```

---

## 🏆 8. Experiment Log

| 실험      | 변경 내용                        | 결과           |
| ------- | ---------------------------- | ------------ |
| Exp 1   | Baseline 재현                  | [점수 기록]      |
| Exp 2   | Model + IMAGE_SIZE 변경        | [상승]         |
| Exp 3   | 데이터 수 + LoRA tuning          | [상승]         |
| ✅ Final | + 영어 Prompt + Fine-tuning 적용 | **최고 점수 달성** |

> 점수는 제출 파일 기준 or leaderboard 점수 작성

---

## 📚 9. References

* Base Code: `251023_Baseline.ipynb`
* Our Best Code: `E026_NULL 먹던걸로.ipynb`
* HuggingFace Models: Qwen/Qwen3-VL-4B-Instruct

---