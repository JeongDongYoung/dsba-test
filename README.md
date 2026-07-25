# Qwen2.5 Math Reasoning SFT

| | |
|:---|:---|
| **학습 데이터** | [`qwedsacf/grade-school-math-instructions`](https://huggingface.co/datasets/qwedsacf/grade-school-math-instructions) &nbsp;(외부 데이터 미사용) |
| **학습 모델** | [`Qwen2.5-0.5B`](https://huggingface.co/Qwen/Qwen2.5-0.5B) · [`Qwen2.5-1.5B`](https://huggingface.co/Qwen/Qwen2.5-1.5B) &nbsp;— base *(Instruct 학습 금지)* |
| **평가** | [`allenai/math_qa`](https://huggingface.co/datasets/allenai/math_qa) — [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness)의 `mathqa` (5지선다 acc) |
| **baseline** | [`Qwen2.5-0.5B-Instruct`](https://huggingface.co/Qwen/Qwen2.5-0.5B-Instruct) · [`Qwen2.5-1.5B-Instruct`](https://huggingface.co/Qwen/Qwen2.5-1.5B-Instruct) &nbsp;*(학습 없이 평가만)* |
| **환경 · 로그** | Colab T4 · [wandb](https://wandb.ai/dongyoung/dsba-coding-test-math-sft) |

## 결과 (mathqa acc)

| 모델 | 방식 | acc | acc_norm |
|:---|:---:|---:|---:|
| Qwen2.5-0.5B-Instruct | - | 0.2898 | 0.2982 |
| Qwen2.5-0.5B *(ours)* | Full&nbsp;FT | **0.3122** | 0.3079 |
| Qwen2.5-0.5B *(ours)* | QLoRA | 0.3106 | **0.3159** |
| Qwen2.5-1.5B-Instruct | - | 0.3370 | 0.3427 |
| Qwen2.5-1.5B *(ours)* | QLoRA | **0.4007** | **0.4067** |

## 저장소 구조

```
.
├── DSBA_coding_test.ipynb   # 데이터 → 학습 → 평가 파이프라인
├── RESULTS.md               # 결과표 및 해석
├── summary_results.md       # mathqa 점수 요약 표
└── results/                 # 평가 결과 JSON
```
