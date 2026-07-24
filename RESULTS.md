# 평가 결과 및 해석

## 최종 성능 (mathqa acc · test 2,985문항)

| 모델 | 방식 | acc | acc_norm |
|:---|:---:|---:|---:|
| Qwen2.5-0.5B-Instruct | baseline | 0.2898 | 0.2982 |
| Qwen2.5-0.5B (ours) | Full FT | 0.3122 | 0.3079 |
| Qwen2.5-0.5B (ours) | QLoRA | 0.3106 | 0.3159 |
| Qwen2.5-1.5B-Instruct | baseline | 0.3370 | 0.3427 |
| Qwen2.5-1.5B (ours) | QLoRA | 0.4007 | 0.4067 |

- 학습본 3개 모두 base·Instruct baseline 상회
- 1.5B QLoRA 0.4007 — Instruct(0.3370) 대비 +0.064
- SE ≈ ±0.008 · 랜덤 하한 0.20

## 학습 History

| 단계 | 0.5B FT | 0.5B QLoRA | 1.5B |
|:---|:---:|:---:|:---:|
| 원본 SFT (통제군) | 0.2556 | 0.2814 | 0.3387 |
| answer-first | 0.3122 | 0.3106 | 0.4007 |
| base / Instruct | 0.2874 / 0.2898 | · | 0.3464 / 0.3370 |

버전별 반복:

- **v1 · 기본 SFT** — 학습 후반 eval loss가 계속 감소 → 미수렴으로 의심
- **v2 · 스텝 확장** — 스텝·에폭을 늘렸으나 개선 없음(1.5B 정체, 0.5B Full FT는 오히려 악화). wandb에서 후반 과적합 재상승 확인 → 미수렴 가설 기각
- **v3 · load_best + 포맷 완화** — 최적 체크포인트 자동 선택(`load_best_model_at_end`) + 짧은 답 혼합 시도. 여전히 baseline 미달 → 원인이 **포맷 불일치**임을 좁힘
- **v4 · answer-first** — 최종 정답을 응답 맨 앞으로 재배치(핵심) → 학습본 3개 전부 baseline 상회로 반전
- **v5 · step 단축** — 1.5B 1200 step 과적합(eval loss U자, wandb) → max_steps 600(정규화 병행) → 최종 0.4007

0.5B는 통제군 기준 Full FT(0.2556) < QLoRA(0.2814) — 소형·소량에서 forgetting이 더 컸으나 answer-first 후엔 비슷.

## 설정

- 데이터 — grade-school-math-instructions 8,492 / 300 분할 · answer-first
- 학습 — LoRA r16·α32 · lr 2e-5(FT)/2e-4(LoRA) · eff. batch 16 · cosine+warmup 3% · load_best_model_at_end
- max_steps — 1.5B 600 / 나머지 1200
- 평가 — lm-evaluation-harness · allenai/math_qa(mathqa) · Colab T4
- wandb — https://wandb.ai/dongyoung/dsba-coding-test-math-sft
