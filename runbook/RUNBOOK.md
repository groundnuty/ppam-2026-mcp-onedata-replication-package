# Reproducibility runbook

This runbook contains the full federation-provisioning, model-endpoint, run-command, test-suite, and audit-trail detail needed to reproduce the K=8 headline. The camera-ready paper retains a one-paragraph Reproducibility note before the Acknowledgements; this file is what that note points at.

## Repository pin

The MCP server, the benchmark harness, and the rescore tooling live in a separate repository pinned at a specific commit:

| | |
|---|---|
| **Repository** | https://github.com/groundnuty/onedata-mcp |
| **Branch**     | `ppam2026/14-tools` |
| **Commit**     | `08e201b` |

The per-trial audit-trail (1008 trials) is in this replication package at [`../artefacts/20260503T002305_k8/`](../artefacts/20260503T002305_k8/) — see also [`../code-pin/README.md`](../code-pin/README.md) for the rationale behind the two-repository split.

## Federation provisioning

```bash
git clone https://github.com/groundnuty/onedata-mcp.git
cd onedata-mcp && git checkout 08e201b
cp .env.template .env  # fill in the federation token + endpoints

make spaces-create      # one per-LLM Onedata space
make spaces-support     # provider attach (idempotent)
```

The federation-token expiration is documented in the `.env` template and must be refreshed before any rerun. Fixture SHAs are pinned in the run-id snapshot so two reruns against the same fixture produce the same starting state.

## Model endpoints

The seven panel LLMs are reachable through three classes of endpoint, all configured via `.env` entries:

| LLM                       | Endpoint class            | Notes                                                                              |
|---------------------------|---------------------------|------------------------------------------------------------------------------------|
| Claude Sonnet 4.5         | `claude-agent-sdk`        | Local Claude Code session against Anthropic's API                                  |
| Google Gemma-4-31B-it     | SanoVelo (local vLLM)     | Port 8002, `--reasoning-parser gemma4`                                             |
| IBM Granite-4.1-30B       | SanoVelo (local vLLM)     | Port 8003, `--reasoning-parser gemma4 --tool-call-parser granite4`                 |
| Mistral Devstral-2-123B   | SanoVelo (local vLLM)     | Port 8004, `max_tokens=8192`                                                       |
| Alibaba Qwen3.6-35B       | OpenAI-compatible HTTPS   | Cyfronet PLGrid Forge                                                              |
| Z.ai GLM-4.7-Flash        | OpenAI-compatible HTTPS   | Cyfronet PLGrid Forge                                                              |
| DeepSeek-V4-Pro           | OpenAI-compatible HTTPS   | OpenRouter → SiliconFlow                                                           |

SanoVelo is the vLLM-based open-LLM inference cluster at Sano. PLGrid Forge is Cyfronet's OpenAI-compatible LLM endpoint.

## Run commands

```bash
make sweep-k8                              # K=8 sweep, all 7 LLMs × 18 scenarios × 8 trials  (~7.5 h sequential)
make show-grid-rate RID=<run-id>           # renders the per-(LLM, scenario) grid
make show-headline RID=<run-id>            # renders the per-LLM headline counts
make rescore RID=<run-id>                  # applies the parser-bug + deployment-artefact lifts
```

The `<run-id>` is the date-stamped directory name printed by `make sweep-k8` (e.g. `20260503T002305_k8`).

## Test suite

`make test` runs **203 unit tests** across the MCP server and benchmark harness. They cover every tool's REST shape, the federation-reset protocol, the oracles, the rescore lift logic, and the harness's tool-allowlist enforcement.

The MCP conformance suite (`modelcontextprotocol/conformance` v0.1.16) reports **2 / 2 PASS** on the `dns-rebinding-protection` scenario after the M-13 transport hardening; rerun via `make conformance`.

## Per-trial data layout

The `artefacts/20260503T002305_k8/` directory in this replication package carries:

- **126 `<llm>__<scenario>.jsonl`** files — per-(LLM, scenario) trial records, 8 trials each, **1008 total**
- **126 `<llm>__<scenario>.rescored.jsonl`** files — same trial set, with parser-bug and deployment-artefact lift annotations
- **`sweep-k8.log`** — full driver log
- **`_per-step-logs/`** — 9 per-step logs (per-LLM K=8 + V4-Pro probe + final report)
- **`REPORT_paper.md`** + **`REPORT_cyfronet.md`** — auto-generated headline summaries

Every model failure cited in §5 of the paper resolves to a specific trial record in this directory. The JSONL schema is documented in [`../artefacts/20260503T002305_k8/README.md`](../artefacts/20260503T002305_k8/README.md).
