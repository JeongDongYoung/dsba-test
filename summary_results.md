# mathqa (allenai/math_qa) 평가 결과 요약

test split 전체 2,985문항 · lm-evaluation-harness · Colab T4

| 모델 | 방식 | acc | acc_norm |
|:---|:---|---:|---:|
| Qwen2.5-0.5B-Instruct | - | 0.2898 | 0.2982 |
| Qwen2.5-0.5B (학습본) | Full FT | 0.3122 | 0.3079 |
| Qwen2.5-0.5B (학습본) | QLoRA | 0.3106 | 0.3159 |
| Qwen2.5-1.5B-Instruct | - | 0.3370 | 0.3427 |
| Qwen2.5-1.5B (학습본) | QLoRA | 0.4007 | 0.4067 |

*참고
- base(미학습): 0.5B 0.2874 / 1.5B 0.3464.
- 순수 SFT: 0.5B FT 0.2556 / 0.5B QLoRA 0.2814 / 1.5B 0.3387.

wandb: https://wandb.ai/dongyoung/dsba-coding-test-math-sft
