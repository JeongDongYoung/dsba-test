# Qwen2.5 Math Reasoning SFT

DSBA Lab 박사과정 코딩테스트. Qwen2.5(base) SFT로 `mathqa`에서 공식 Instruct baseline 넘기기.

| | |
|:---|:---|
| **학습 데이터** | [`qwedsacf/grade-school-math-instructions`](https://huggingface.co/datasets/qwedsacf/grade-school-math-instructions) &nbsp;(외부 데이터 미사용) |
| **학습 모델** | [`Qwen2.5-0.5B`](https://huggingface.co/Qwen/Qwen2.5-0.5B) · [`Qwen2.5-1.5B`](https://huggingface.co/Qwen/Qwen2.5-1.5B) &nbsp;— base *(Instruct 학습 금지)* |
| **평가** | [`allenai/math_qa`](https://huggingface.co/datasets/allenai/math_qa) — [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness)의 `mathqa` (5지선다 acc) |
| **baseline** | [`Qwen2.5-0.5B-Instruct`](https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct) · [`Qwen2.5-1.5B-Instruct`](https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct) &nbsp;*(학습 없이 평가만)* |
| **환경 · 로그** | Colab T4 · [wandb](https://wandb.ai/dongyoung/dsba-coding-test-math-sft) 로깅 · 산출물은 Google Drive |

## 결과 (mathqa acc)

| 모델 | 방식 | acc | acc_norm |
|:---|:---:|---:|---:|
| Qwen2.5-0.5B-Instruct | baseline | 0.2898 | 0.2982 |
| Qwen2.5-0.5B *(ours)* | Full&nbsp;FT | **0.3122** | 0.3079 |
| Qwen2.5-0.5B *(ours)* | QLoRA | 0.3106 | **0.3159** |
| Qwen2.5-1.5B-Instruct | baseline | 0.3370 | 0.3427 |
| Qwen2.5-1.5B *(ours)* | QLoRA | **0.4007** | **0.4067** |

<sub>굵게 = 각 규모에서 최고값 · (ours) = 직접 학습본 · mathqa test 2,985문항 (5지선다, 랜덤 하한 0.20, SE ≈ ±0.008)</sub>

- 학습본 3개 모두 baseline 상회 — 1.5B는 Instruct 대비 +0.064
- 통제군(포맷 정렬 전 순수 SFT): 0.5B FT 0.2556 · 1.5B 0.3387 — base보다도 낮음
- 성능 병목 = 학습–평가 포맷 불일치 (모델 크기 아님) → answer-first 정렬로 반전. 상세: [`RESULTS.md`](RESULTS.md)

## 저장소 구조

```
.
├── DSBA_coding_test.ipynb   # 데이터 → 학습 → 평가 파이프라인
├── RESULTS.md               # 결과표 및 해석
├── summary_results.md       # mathqa 점수 요약 표
└── results/                 # 평가 결과 JSON
```

## 접근

파이프라인: 데이터 준비 → 학습 `train_model()` → 평가 `evaluate_model()` → 결과

- answer-first 정렬 — 최종 정답을 응답 맨 앞으로 재배치해 mathqa 채점 포맷에 맞춤 (성능의 핵심 요인)
- 모델별 전략 — 0.5B는 Full FT(+ QLoRA 비교), 1.5B는 메모리 제약으로 4-bit QLoRA
- 1.5B max_steps 600 — 1200 step 학습 시 후반 eval loss 재상승(과적합)을 회피
- fp16 고정 — T4는 bf16 미지원, 체크포인트가 bf16이라 `dtype=torch.float32`로 명시 로드
- mathqa 패치 — `math_qa` 경로가 Hub에서 제거됨. `allenai/math_qa` 커스텀 태스크로 대체하고 `datasets==2.19.2` 고정
