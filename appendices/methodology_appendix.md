# Methodology appendix — reference tables

This appendix carries two reference tables that the camera-ready paper points at: the metrics-summary table (symbols, units, formulas, sources) and the per-task oracle classification (which of the 18 scenarios use which oracle tier).

The main paper retains the two-axis OracleResult, panel composition, lift policy, and the pass⁸ formula. The per-LLM-space architecture and the federation-reset protocol are summarised below for self-contained reading; the implementation lives in the engineering repo (see [`../code-pin/README.md`](../code-pin/README.md)).

## Metrics summary

All counters are computed per trial and then averaged over the eighteen scenarios for the panel-aggregate value. Symbols match the paper.

| Symbol           | Unit          | Formula                                                      | Source            |
|------------------|---------------|--------------------------------------------------------------|-------------------|
| pass⁸            | proportion    | E_task[ C(c, k) / C(n, k) ] with k = 8, n = 8                | Yao 2024 (τ-bench)|
| TC               | tool calls    | mean over trials                                             | own               |
| WT               | seconds       | mean over trials (wall-clock)                                | own               |
| T_in             | tokens        | mean over trials                                             | Song 2025         |
| T_out            | tokens        | mean over trials                                             | Song 2025         |
| H_name           | proportion    | \|{c: name ∉ tool-set}\| / N                                 | Yao 2024          |
| H_param          | proportion    | \|{c: params malformed}\| / \|{c: name ok}\|                 | Yao 2024          |
| H_qos            | proportion    | \|{INVALID_QOS_EXPR}\| / N_placement                         | own               |
| TCS              | tokens/win    | (T_in + T_out) / pass¹                                       | Liu 2025 (inverse Token Efficiency) |

## Per-task oracle classification (18 scenarios)

Three tiers per the taxonomy in MCP-Universe (Luo et al. 2025): **format** parses the agent's answer text only; **static** also reads the federation post-state through a REST side-channel; **dynamic** polls federation state with a deadline.

| ID | Band              | Brief (one-liner)                                  | Oracle tier |
|----|-------------------|----------------------------------------------------|-------------|
| D1 | Discovery         | List spaces with provider counts                   | format      |
| D2 | Discovery         | Find files matching pattern in space X             | format      |
| D3 | Discovery         | Read file content and report size                  | format      |
| D4 | Discovery         | List providers supporting space X                  | format      |
| D5 | Discovery         | Get JSON metadata for path *P*                     | format      |
| D6 | Discovery         | Trivial: list spaces (sanity check)                | format      |
| A1 | Access            | Tag every raw file under `/datasets/X`             | format      |
| A2 | Access            | Set `reviewed=false` on every file in subtree      | static      |
| A3 | Access            | Rename then re-tag a file                          | static      |
| A4 | Access            | Cross-directory copy with metadata                 | static      |
| A5 | Access            | Multi-step QoS-aware write                         | static      |
| A6 | Access            | Recursive metadata predicate query                 | format      |
| P1 | Placement         | Where stored — fully replicated to EU?             | static      |
| P2 | Placement         | QoS-violation diagnostic                           | static      |
| P3 | Placement         | Force ≥ 2 EU replicas (polling)                    | dynamic     |
| P4 | Placement         | Most-recent migration of file *F*                  | dynamic     |
| P5 | Placement         | QoS conflict resolution                            | static      |
| P6 | Placement         | replica_count == 1 introspection                   | static      |

Distribution: 8 format / 8 static / 2 dynamic, reflecting that the federation state is dominantly observable through direct REST. Full briefs, fixtures, pass conditions, and fail modes are in [`scenarios.md`](scenarios.md).

## Per-LLM-space architecture (one-paragraph summary)

Each panel LLM is given its own Onedata space (named `ppam_2026_mcp_tests_<llm>`); the scenario fixtures substitute the per-LLM space name at fixture-load time. Onedata spaces are first-class isolation boundaries that share the federation's identity authority but not the namespace, so seven concurrent LLM runs on the same federation cannot interfere through filesystem operations. Each panel-LLM space is supported by both Oneproviders (`cloud-pl` and `Cloud-SK`), so the federation topology each agent sees is identical.

## Federation-reset 5-phase protocol (one-paragraph summary)

Between every trial the harness wipes the scenario's subtree (`/<space>/<scenario_id>/`) and reconstructs the fixture in five phases — (1) cancel any active transfers on the subtree, (2) remove agent-added QoS rules, (3) delete scratch files, (4) restore fixture metadata, (5) re-balance replicas across the supporting providers — polling witness files until two consecutive polls match the fixture and no QoS rule is in `pending` state. The reset has a hard cap of 120 s and three-attempt retry with exponential backoff; timeouts re-queue the trial to the back of the schedule rather than failing the cell.
