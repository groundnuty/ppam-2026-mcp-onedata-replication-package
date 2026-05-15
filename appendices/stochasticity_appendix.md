# Stochasticity and tool-removal feasibility

This appendix carries the full cell-stability decomposition (Table 9 in earlier paper drafts) and the tool-removal feasibility argument; the camera-ready paper retains a single-sentence reference and points here for the detail.

## Stable, stochastic, and stable-fail cells

Reliability decomposes by cell-stability. Two LLMs (Sonnet, Gemma-4) have all eighteen cells stable-PASS. The next tier — DeepSeek-V4-Pro, Qwen3.6, Devstral, GLM, Granite — has between eleven and sixteen stable-PASS cells, with the remainder stochastic.

**Only one cell across the entire 126-pair grid is stable-FAIL: Granite P1.** The agent calls `get_file_distribution` successfully but does not connect the canonical provider-name strings `cloud-pl` and `Cloud-SK` with their EU geography, so the answer reports the per-provider distribution correctly but never names the file as fully replicated to EU providers. This is a model-knowledge gap, not a tool-surface gap, and is dissected in the paper's §5 (capability-gap pattern C-3).

The decomposition makes the n = 8 stochasticity visible at cell granularity. A single pass⁸ aggregate would mix cells that flip 7-of-8 with cells that flip 3-of-8.

| Model                     | Stable-PASS | Stable-FAIL | Stochastic |
|---------------------------|:-----------:|:-----------:|:----------:|
| Claude Sonnet 4.5         | 18 / 18     | 0           | 0          |
| Google Gemma-4-31B-it     | 18 / 18     | 0           | 0          |
| DeepSeek-V4-Pro           | 16 / 18     | 0           | 2          |
| Alibaba Qwen3.6-35B       | 16 / 18     | 0           | 2          |
| Mistral Devstral-2-123B   | 12 / 18     | 0           | 6          |
| Z.ai GLM-4.7-Flash        | 11 / 18     | 0           | 7          |
| IBM Granite-4.1-30B       | 11 / 18     | 1 (P1)      | 6          |

*Stable-PASS = all 8 trials pass. Stable-FAIL = all 8 trials fail. Stochastic = mixed pass/fail.*

## Tool-removal feasibility

The curation choices that gave us the 16-tool MCP surface are also testable in the negative direction: which scenarios remain *structurally* feasible if the federation-state tools are removed and only file CRUD remains? Of the 18 scenarios, only four:

| Band                                            | Feasible on CRUD-only | Examples                                              |
|-------------------------------------------------|:---------------------:|-------------------------------------------------------|
| Discovery (D1–D6)                               | *K_D* = 1 of 6        | D6 (sanity: list space names)                         |
| Multi-step access (A1–A6)                       | *K_A* = 3 of 6        | A1, A2, A3 (metadata tagging via file-by-file writes) |
| Placement-introspection (P1–P6)                 | *K_P* = 0 of 6        | (no agent can reason about replica geography, QoS rules, or transfer history from file CRUD alone) |

These *K*-counts are **pre-registered**: they follow deterministically from the tool-availability constraints and do not depend on which model runs. The full empirical n = 8 sweep on the CRUD-only subset, against the headline 16-tool surface, is left to future work where the four-feasible-task budget can be paired with a higher *n* for meaningful statistical power.
