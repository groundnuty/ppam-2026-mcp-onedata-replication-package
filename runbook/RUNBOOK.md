# Reproducibility runbook

*Cut from §9 Reproducibility. The main paper retains a single-paragraph Reproducibility note before Acknowledgements; this RUNBOOK contains the full federation-provisioning, model-endpoint, run-command, test-suite, and audit-trail detail.*

## Repository pin

The MCP server, the benchmark harness, and the per-trial audit-trail are released alongside this paper at `github.com/groundnuty/onedata-mcp` on branch `ppam2026/14-tools` at commit `08e201b`. Reproducing the K=8 headline requires the federation provisioning, the model endpoints, and the run commands below.

## Full 5-paragraph reproducibility detail (LaTeX-source verbatim)

```latex
\section{Reproducibility}
\label{sec:repro}

The MCP server, the benchmark harness, and the per-trial audit-trail are released alongside this paper at \texttt{github.com/groundnuty/onedata-mcp} on branch \texttt{ppam2026/14-tools} at commit \texttt{08e201b}. Reproducing the K=8 headline requires the federation provisioning, the model endpoints, and the run commands.

\paragraph{Federation provisioning.} \texttt{make spaces-create \&\& make spaces-support} provisions the per-LLM Onedata spaces against an existing federation; the SPICE-federation token expiration is documented in the \texttt{.env} template and must be refreshed pre-camera-ready. The fixture SHAs are pinned in the run-id snapshot.

\paragraph{Model endpoints.} The seven panel LLMs are reachable via three classes of endpoint, all configured via \texttt{.env} template entries. Sonnet runs through the \texttt{claude-agent-sdk} on a local Claude Code session. SanoVelo~\cite{sano2025velo} --- Sano's vLLM-based~\cite{kwon2023vllm} open-LLM inference cluster --- serves Gemma-4 (port 8002 with \texttt{--reasoning-parser gemma4}), Granite-4.1 (port 8003 with \texttt{--reasoning-parser gemma4 --tool-call-parser granite4}), and Devstral (port 8004 with \texttt{max\_tokens=8192}). OpenAI-compatible HTTPS endpoints serve Qwen3.6, GLM-4.7-Flash (Cyfronet PLGrid Forge~\cite{cyfronet2025plgridforge}), and DeepSeek-V4-Pro (OpenRouter $\to$ SiliconFlow).

\paragraph{Run commands.} \texttt{make sweep-k8} runs the K=8 sweep across the seven-LLM panel on the eighteen-scenario suite; total wall-time is approximately 7.5~hours. \texttt{make show-grid-rate RID=<run-id>} renders the per-cell grid (the source for Table~\ref{tab:per-cell}); \texttt{make show-headline RID=<run-id>} renders the headline tables.

\paragraph{Test suite.} \texttt{make test} runs 203 unit tests across the MCP server and benchmark harness. The conformance suite~\cite{mcpconformance2026} v0.1.16 reports 2/2 PASS on \texttt{dns-rebinding-protection} after the M-13 transport hardening; it can be re-run via \texttt{make conformance}.

\paragraph{Per-trial data.} The \texttt{artefacts/20260503T002305\_k8/} directory carries the per-(LLM, scenario) trial records (1008 in total), the auto-generated \texttt{REPORT\_paper.md} headline summary, and the per-LLM diagnostic \texttt{REPORT\_cyfronet.md}. Every model failure cited in Section~\ref{sec:findings} resolves to a specific trial record in this directory.

% ============================================================
```
