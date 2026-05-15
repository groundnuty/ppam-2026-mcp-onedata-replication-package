# PPAM 2026 benchmark — paper summary  (run `20260503T002305_k8`)

**Panel:** claude-sonnet-4-5, deepseek-v4-pro, devstral-2-123b, gemma-4-31b-it, glm-4.7-flash, granite-4.1-30b, qwen3.6-35b  •  **K (max trials/cell):** 8

**Scenarios run:** 18 (A1, A2, A3, A4, A5, A6, D1, D2, D3, D4, D5, D6, P1, P2, P3, P4, P5, P6)


## Per-cell pass-rate (PASS-count / trials)

| Scenario | claude-sonnet-4-5 | deepseek-v4-pro | devstral-2-123b | gemma-4-31b-it | glm-4.7-flash | granite-4.1-30b | qwen3.6-35b |
|---|---|---|---|---|---|---|---|
| A1 | 8/8 | 8/8 | 7/8 | 8/8 | 8/8 | 5/8 | 8/8 |
| A2 | 8/8 | 8/8 | 6/8 | 8/8 | 6/8 | 1/8 | 6/8 |
| A3 | 8/8 | 6/8 | 5/8 | 8/8 | 8/8 | 3/8 | 8/8 |
| A4 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 7/8 | 8/8 |
| A5 | 8/8 | 8/8 | 5/8 | 8/8 | 4/8 | 8/8 | 8/8 |
| A6 | 8/8 | 8/8 | 6/8 | 8/8 | 8/8 | 8/8 | 8/8 |
| D1 | 8/8 | 8/8 | 2/8 | 8/8 | 4/8 | 0/8 | 8/8 |
| D2 | 8/8 | 8/8 | 8/8 | 8/8 | 7/8 | 8/8 | 8/8 |
| D3 | 8/8 | 8/8 | 8/8 | 8/8 | 6/8 | 8/8 | 8/8 |
| D4 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 |
| D5 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 7/8 |
| D6 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 7/8 | 8/8 |
| P1 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 0/8 | 8/8 |
| P2 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 |
| P3 | 8/8 | 7/8 | 8/8 | 8/8 | 6/8 | 4/8 | 8/8 |
| P4 | 8/8 | 8/8 | 8/8 | 8/8 | 5/8 | 7/8 | 8/8 |
| P5 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 | 8/8 |
| P6 | 8/8 | 8/8 | 7/8 | 8/8 | 7/8 | 8/8 | 7/8 |

## Per-LLM totals (stability breakdown)

| LLM | trials | PASS | FAIL | RESET | ADAPTER | rate | stable PASS | stable FAIL | stochastic |
|---|---|---|---|---|---|---|---|---|---|
| `claude-sonnet-4-5` | 144 | 144 | 0 | 0 | 0 | 100.0% | 18/18 | 0/18 | 0/18 |
| `deepseek-v4-pro` | 144 | 141 | 3 | 0 | 0 | 97.9% | 16/18 | 0/18 | 2/18 |
| `devstral-2-123b` | 144 | 126 | 18 | 0 | 0 | 87.5% | 11/18 | 0/18 | 7/18 |
| `gemma-4-31b-it` | 144 | 144 | 0 | 0 | 0 | 100.0% | 18/18 | 0/18 | 0/18 |
| `glm-4.7-flash` | 144 | 125 | 19 | 0 | 0 | 86.8% | 10/18 | 0/18 | 8/18 |
| `granite-4.1-30b` | 144 | 106 | 38 | 0 | 0 | 73.6% | 9/18 | 2/18 | 7/18 |
| `qwen3.6-35b` | 144 | 140 | 4 | 0 | 0 | 97.2% | 15/18 | 0/18 | 3/18 |

## Stochastic cells (0 < P < K)

Cells with mixed outcomes — these are where harness, model, or deployment variance shows up. Stable-PASS / stable-FAIL cells (not listed) are deterministic at this K.

- **`deepseek-v4-pro`**: `A3`=6/8, `P3`=7/8
- **`devstral-2-123b`**: `A1`=7/8, `A2`=6/8, `A3`=5/8, `A5`=5/8, `A6`=6/8, `D1`=2/8, `P6`=7/8
- **`glm-4.7-flash`**: `A2`=6/8, `A5`=4/8, `D1`=4/8, `D2`=7/8, `D3`=6/8, `P3`=6/8, `P4`=5/8, `P6`=7/8
- **`granite-4.1-30b`**: `A1`=5/8, `A2`=1/8, `A3`=3/8, `A4`=7/8, `D6`=7/8, `P3`=4/8, `P4`=7/8
- **`qwen3.6-35b`**: `A2`=6/8, `D5`=7/8, `P6`=7/8
