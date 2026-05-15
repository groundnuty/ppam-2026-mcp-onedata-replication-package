# n = 8 headline run artefacts — `20260503T002305_k8`

This directory contains the per-trial audit-trail for the headline `n = 8` sweep that produced the **956 / 1008 = 94.8 %** MCP-effectiveness figure reported in the paper.

- **Run start:** `2026-05-03 00:23:05 UTC`
- **Wall-time:** ≈ 7.5 h sequential
- **Panel:** 7 LLMs × 18 scenarios × 8 trials = **1008 trials**
- **Producer:** `groundnuty/onedata-mcp@ppam2026/14-tools` at commit **`08e201b`** (see [`../../code-pin/README.md`](../../code-pin/README.md))

## File layout

| Path | Count | Purpose |
|---|---|---|
| `<llm>__<scenario>.jsonl`           | 126 | Per-(LLM, scenario) trial records, **as-measured** — one JSON object per trial (8 trials per file) |
| `<llm>__<scenario>.rescored.jsonl`  | 126 | Same trial set, **with rescore sidecar fields** appended — captures parser-bug lifts and deployment-artefact lifts |
| `sweep-k8.log`                       | 1   | The full driver log produced by `make sweep-k8` |
| `_per-step-logs/0N-<llm>.log`        | 9   | One log per panel step (per-LLM n = 8 invocation + V4-Pro probe + final report step) |

The 126 = (7 LLMs × 18 scenarios) pairing is exhaustive. The 8 trials per cell are concatenated into a single line-delimited JSON file per cell.

## JSONL schema — `<llm>__<scenario>.rescored.jsonl`

Each line is one trial. The schema is a superset of the as-measured schema: every field present in `<llm>__<scenario>.jsonl` is also present in the rescored sidecar.

### Trial-identity fields

| Field | Type | Notes |
|---|---|---|
| `run_id` | str | `"20260503T002305_k8"` for every record in this directory |
| `llm_name` | str | One of: `claude-sonnet-4-5`, `gemma-4-31b-it`, `deepseek-v4-pro`, `qwen3.6-35b`, `devstral-2-123b`, `glm-4.7-flash`, `granite-4.1-30b` |
| `llm_model_id` | str | Concrete pinned id (e.g.\ `claude-sonnet-4-5-20250929`) |
| `scenario_id` | str | One of `D1`..`D6`, `A1`..`A6`, `P1`..`P6` |
| `trial_ix` | int | 0..7 within the n = 8 sweep |

### Timing fields

| Field | Type | Notes |
|---|---|---|
| `started_at`, `ended_at` | float (epoch s) | Trial wall-time bounds |
| `fixture_started_at`, `fixture_ready_at` | float | Federation-reset bounds (5-phase protocol; see [`../../appendices/methodology_appendix.md`](../../appendices/methodology_appendix.md)) |
| `wall_clock_seconds` | float | `ended_at - started_at` |

### Agent execution fields

| Field | Type | Notes |
|---|---|---|
| `tool_calls` | list[dict] | Sequence of `{tool_name, arguments, succeeded, error, latency_seconds}` |
| `final_answer` | str | The agent's final natural-language response |
| `rounds_used` | int | Number of LLM rounds within the trial budget |
| `finish_reason` | str | `success` / `tool_error` / `timeout` / etc. |
| `usage_in_tokens`, `usage_out_tokens` | int | Reported by the provider |
| `denied_calls` | list | Any tool calls the harness refused (out-of-allowlist) |
| `error` | str/null | Trial-level error (transport, oracle exception, etc.) |
| `raw_trace` | dict | Full provider trace where available; empty `{}` for legs that did not surface one |

### As-measured oracle fields

| Field | Type | Notes |
|---|---|---|
| `oracle_mcp_pass` | bool | The agent-side correctness axis — did the tool-call sequence + final answer satisfy the brief? |
| `oracle_federation_pass` | bool/null | The federation-side post-state axis — does the live REST view match the expected post-state? `null` for format-tier scenarios |
| `oracle_diagnosis` | str | Free-text diagnosis when a trial fails |
| `outcome` | str | `"PASS"` iff `oracle_mcp_pass == True`, else `"FAIL"`. Always uses the as-measured `oracle_mcp_pass`. |

### Rescore sidecar fields (present only in `.rescored.jsonl`)

The rescore script applies two non-destructive lift passes — **never demoting** a passing trial. Original fields are preserved with `_original` suffix; new evaluations are recorded as `_v2`.

| Field | Type | Notes |
|---|---|---|
| `oracle_mcp_pass_original` | bool | Verbatim copy of the as-measured `oracle_mcp_pass` |
| `oracle_mcp_pass_rescore_raw` | bool | What the **fixed oracle** alone gives — may legitimately be `False` even when the original was `True` (federation state has moved on; the lift logic refuses to act on it) |
| `oracle_mcp_pass_v2` | bool | Final post-clean verdict: `True` iff (original was `True`) OR (rescore-raw is `True`) OR (deployment-artefact lift applies). Never demotes. |
| `oracle_diagnosis_original` | str | Verbatim copy of as-measured diagnosis |
| `oracle_diagnosis_v2` | str | Diagnosis post-lift |
| `outcome_original` | str | `"PASS"`/`"FAIL"` from `oracle_mcp_pass_original` |
| `outcome_v2` | str | `"PASS"`/`"FAIL"` from `oracle_mcp_pass_v2` |
| `rescore_version` | str | Lift-protocol tag (`"2026-05-03-helpers-v2"` for this run) |
| `rescore_changed` | bool | `True` iff `outcome_v2 != outcome_original` |
| `rescore_demotion_rejected` | bool | `True` iff the rescore would have demoted `PASS → FAIL` and was refused |
| `lift_kind` | str/null | `"parser-bug"`, `"deployment-artefact"`, or `null` if no lift was applied |
| `failure_category` | str/null | Post-clean classification for unlifted failures: `"model"`, `"deployment-L3-granite-tool-call"`, or `null` for passing trials and lifted trials |

### Rescore protocol summary

1. **Parser-bug lift** — re-runs the oracle with three fixes to `benchmark/oracles/_helpers.py`: case-insensitive key matching, 3-column-table tolerance, and JSON-shape answer handling. Lifts FAIL→PASS where the corrected oracle agrees and the original disagreement was a parser bug. Lifted 20 trials.
2. **Deployment-artefact lift** — detects the L-3 `<tool_call>` markup leak from Granite's vLLM tool-call parser. Lifts FAIL→PASS where the agent's MCP intent was correct but the serving-layer corrupted the trace. Lifted 10 trials.
3. **Never demote.** If the rescore oracle says PASS→FAIL, the rescore is refused (`rescore_demotion_rejected: true`); the original PASS verdict stands. This is load-bearing because the rescore runs offline against stubbed federation state, which is not the same as the live federation state at trial time.

For the methodology in detail, see [`../../appendices/methodology_appendix.md`](../../appendices/methodology_appendix.md) and the data-cleaning rationale in the [project memory referenced in `../../README.md`](../../README.md#further-reading).

## Reproducing the headline figure

```bash
# 1. Get the MCP fork at the pinned commit
git clone https://github.com/groundnuty/onedata-mcp.git
cd onedata-mcp && git checkout 08e201b

# 2. Set up federation credentials per ../../runbook/RUNBOOK.md

# 3. Run the headline sweep (≈ 7.5 h)
make sweep-k8

# 4. Apply the two-layer rescore
make rescore RID=<run-id-printed-by-step-3>

# 5. Read the headline
make show-headline RID=<run-id>
```

The artefacts in this directory are the canonical output of steps 3–4 against the live federation on 2026-05-03. Independent reruns will reproduce the panel rate within stochastic n = 8 confidence intervals.

## How the paper cites these artefacts

- §5 capability-gap citations resolve to specific `<llm>__<scenario>.rescored.jsonl` records — pick a failing trial in the named cell and inspect `final_answer`, `tool_calls`, and `oracle_diagnosis`.
- §5 Granite C-3 P1 stable fail: all 8 of `granite-4.1-30b__P1.jsonl` have `oracle_mcp_pass: false` (and stay false in the rescored sidecar — this is a genuine model failure, not a parser bug or deployment artefact).
- §5 C-1 multi-step `set_file_metadata` sequencing: inspect `tool_calls` count vs the required N on `devstral-2-123b__A{1,2,3,6}.jsonl`, `glm-4.7-flash__A{1,2,3,6}.jsonl`, `granite-4.1-30b__A{1,2,3,6}.jsonl`.

## Hard backup

A byte-for-byte copy of this directory, taken at the moment the rescore protocol was finalised, is preserved separately at the MCP fork — `artefacts/20260503T002305_k8_BACKUP_<timestamp>/`. It is not committed to this replication repo (size + duplication) but exists for the audit trail in the engineering repo.
