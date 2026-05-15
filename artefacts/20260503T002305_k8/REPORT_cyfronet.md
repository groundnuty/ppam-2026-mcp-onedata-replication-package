# PPAM 2026 LLM panel — Cyfronet Forge diagnostic report

Run: `20260503T002305_k8`  •  Total trials: 1008

This report breaks down per-model behaviour observed against the PPAM 2026 benchmark (18-scenario set covering discovery / access / placement bands of Onedata federated data operations). Intended for Cyfronet operations: which Forge-hosted models can be reliably used for tool-using agent workloads, and what specific limitations each exhibits.


## Headline cross-model summary

| Model | PASS | FAIL | API/adapter errors | Avg rounds | Avg in-tokens | Avg out-tokens |
|---|---|---|---|---|---|---|
| `claude-sonnet-4-5` | 144 | 0 | 0 | 13.5 | 52 | 1560 |
| `deepseek-v4-pro` | 141 | 3 | 0 | 4.0 | 29112 | 1080 |
| `devstral-2-123b` | 126 | 18 | 0 | 2.7 | 4742 | 240 |
| `gemma-4-31b-it` | 144 | 0 | 0 | 2.8 | 4374 | 289 |
| `glm-4.7-flash` | 125 | 19 | 0 | 3.6 | 14752 | 1054 |
| `granite-4.1-30b` | 106 | 38 | 0 | 3.6 | 7534 | 237 |
| `qwen3.6-35b` | 140 | 4 | 0 | 3.9 | 24200 | 1785 |

## `claude-sonnet-4-5`

- Pass rate: **144/144** (100%)

- Average wall-clock per trial: 36.1s


### On passing trials
- Avg rounds: 13.5
- Avg input tokens: 52
- Avg output tokens: 1560


### Recommendation

- **No issues observed.** Suitable for tool-using agent workloads.


## `deepseek-v4-pro`

- Pass rate: **141/144** (98%)

- Average wall-clock per trial: 64.6s


### Failure modes (oracle FAIL)

- **2× scenarios:** mcp_pass=False (no set_file_metadata(published, status=published))

- **1× scenarios:** mcp_pass=False (no list_space_transfers or get_file_qos_summary follow-up)


*Tool-call patterns on failures:*

  - 2× `move_file → set_file_metadata`

  - 1× `add_file_qos_requirement → list_user_spaces`


### On passing trials
- Avg rounds: 4.1
- Avg input tokens: 29667
- Avg output tokens: 1093


### Recommendation

- Generally suitable; isolated FAILs are domain-reasoning shortfalls, not Forge issues.


## `devstral-2-123b`

- Pass rate: **126/144** (88%)

- Average wall-clock per trial: 10.2s


### Failure modes (oracle FAIL)

- **4× scenarios:** answer omits space 'ppam_2026_mcp_tests_qwen3_coder_30b'

- **3× scenarios:** mcp_pass=False (no set_file_metadata(published, status=published))

- **3× scenarios:** mcp_pass=False (no add_file_qos_requirement with EU + replicas≥2) federation_pas

- **2× scenarios:** mcp_pass=False (/ppam_2026_mcp_tests_devstral_2_123b/a2/inbox/msg00.txt; /ppam_2

- **2× scenarios:** path-set mismatch: missing=['/ppam_2026_mcp_tests_devstral_2_123b/a6/batch01/f1.

- **2× scenarios:** answer omits space 'BarteksSpace'

- **1× scenarios:** tagged count: expected 5, got None

- **1× scenarios:** mcp_pass=False (set mismatch: missing=['/ppam_2026_mcp_tests_devstral_2_123b/p6/


*Tool-call patterns on failures:*

  - 6× `list_user_spaces`

  - 3× `move_file → set_file_metadata`

  - 3× `create_file → add_file_qos_requirement`

  - 2× `list_files_recursively → set_file_metadata → set_file_metadata → set_file_metadata → set_file_metadata`

  - 2× `query_by_metadata`


### On passing trials
- Avg rounds: 2.7
- Avg input tokens: 4673
- Avg output tokens: 236


### Recommendation

- Generally suitable; isolated FAILs are domain-reasoning shortfalls, not Forge issues.


## `gemma-4-31b-it`

- Pass rate: **144/144** (100%)

- Average wall-clock per trial: 9.5s


### On passing trials
- Avg rounds: 2.8
- Avg input tokens: 4374
- Avg output tokens: 289


### Recommendation

- **No issues observed.** Suitable for tool-using agent workloads.


## `glm-4.7-flash`

- Pass rate: **125/144** (87%)

- Average wall-clock per trial: 8.7s


### Failure modes (oracle FAIL)

- **4× scenarios:** mcp_pass=False (no add_file_qos_requirement with EU + replicas≥2) federation_pas

- **2× scenarios:** mcp_pass=False (/ppam_2026_mcp_tests_glm_4_7_flash/a2/inbox/msg00.txt; /ppam_202

- **2× scenarios:** mcp_pass=False (no add_file_qos_requirement with EU + replicas≥2)

- **2× scenarios:** mcp_pass=False (no get_transfer call; answer doesn't contain captured transferId

- **1× scenarios:** space 'CzeslawsSpace': expected provider_count=1, got None

- **1× scenarios:** space 'CloudSKTest': expected provider_count=3, got None

- **1× scenarios:** space 'test': expected provider_count=2, got None

- **1× scenarios:** space 'IagosSpace': expected provider_count=3, got 5


*Tool-call patterns on failures:*

  - 2× `list_files_recursively → set_file_metadata → set_file_metadata → set_file_metadata → set_file_metadata`

  - 2× `create_file → add_file_qos_requirement`

  - 2× `download_file`

  - 1× `create_file → add_file_qos_requirement → add_file_qos_requirement`

  - 1× `create_file → list_user_spaces → add_file_qos_requirement`


### On passing trials
- Avg rounds: 3.6
- Avg input tokens: 14272
- Avg output tokens: 900


### Recommendation

- Generally suitable; isolated FAILs are domain-reasoning shortfalls, not Forge issues.


## `granite-4.1-30b`

- Pass rate: **106/144** (74%)

- Average wall-clock per trial: 11.1s


### Failure modes (oracle FAIL)

- **8× scenarios:** mcp_pass=False (answer missing one or both provider names)

- **5× scenarios:** mcp_pass=False (no set_file_metadata(published, status=published))

- **4× scenarios:** mcp_pass=False (/ppam_2026_mcp_tests_granite_4_1_30b/a2/inbox/msg02.txt; /ppam_2

- **4× scenarios:** answer omits space 'ppam_2026_mcp_tests_qwq_32b'

- **4× scenarios:** answer omits space 'ppam_2026_mcp_tests_granite_4_1_30b'

- **3× scenarios:** tagged count: expected 5, got None

- **2× scenarios:** mcp_pass=False (/ppam_2026_mcp_tests_granite_4_1_30b/a2/inbox/msg03.txt) federat

- **2× scenarios:** mcp_pass=False (no list_space_transfers or get_file_qos_summary follow-up)


*Tool-call patterns on failures:*

  - 9× `list_user_spaces`

  - 8× `get_file_distribution`

  - 5× `list_files_recursively → set_file_metadata → set_file_metadata`

  - 5× `move_file → set_file_metadata`

  - 3× `list_files_recursively → set_file_metadata → set_file_metadata → set_file_metadata`


### On passing trials
- Avg rounds: 3.6
- Avg input tokens: 6720
- Avg output tokens: 207


### Recommendation

- Generally suitable; isolated FAILs are domain-reasoning shortfalls, not Forge issues.


## `qwen3.6-35b`

- Pass rate: **140/144** (97%)

- Average wall-clock per trial: 12.2s


### Failure modes (oracle FAIL)

- **2× scenarios:** mcp_pass=False (/ppam_2026_mcp_tests_qwen3_6_35b/a2/inbox/msg00.txt; /ppam_2026_

- **1× scenarios:** k:v mismatch for 'pipeline_stage': expected 'raw', got None

- **1× scenarios:** mcp_pass=False (set mismatch: missing=[] unexpected=['/ppam_2026_mcp_tests_qwen3


*Tool-call patterns on failures:*

  - 2× `list_files_recursively → set_file_metadata → set_file_metadata → set_file_metadata → set_file_metadata`

  - 1× `get_file_metadata`

  - 1× `list_files_recursively → get_file_qos_summary → get_file_qos_summary → get_file_qos_summary → get_qos_requirement → get_qos_requirement → get_qos_requirement`


### On passing trials
- Avg rounds: 4.0
- Avg input tokens: 24743
- Avg output tokens: 1815


### Recommendation

- Generally suitable; isolated FAILs are domain-reasoning shortfalls, not Forge issues.


## Forge-API observations

Independent of per-model behaviour, the following Forge-side gaps surfaced during integration:

1. **`/v1/models` does not expose tags.** Function-calling support (`FC`), embedding-vs-chat (`EMB`), grant availability are visible only in the web UI. Agent harnesses can't filter automatically; must enumerate empirically.
2. **`max_tokens` server-side caps vary per model and are not exposed.** Setting a generous budget breaks some models with `BadRequestError`. Per-model headroom should either be discoverable via API or documented per model.
3. **Inactive models still appear in `/v1/models`.** Returning a different shape (or filtering them) would let consumers pre-flight without exhausting trial-and-error.
4. **Identical error message format across catalogue / inactive / grant-permission failures.** All three return HTTP 400 with a `detail` string; programmatic differentiation requires substring matching.
