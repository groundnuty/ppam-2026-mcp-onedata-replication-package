# Positioning tables (cut from PPAM 2026, reserved for FGCS extension)

Both tables were dropped from the PPAM 2026 paper (15-pp strict limit) on
2026-05-15 following a co-author review with Renata Słota. The prose
descriptions in §2 of the PPAM paper enumerate the same benchmarks and
prior work in narrative form. These tables are preserved here verbatim
for the FGCS extension, which has the page budget to support them.

## Table 1 — Positioning vs the 2025 MCP-benchmark wave

Columns: substrate covered, tool count, LLMs evaluated, oracle methodology,
federation scope, and L/M = whether servers are live or LLM-generated mocks.

Shorthand:
- *rule+rubric LLM* = rule-based plus rubric-driven LLM-as-judge scoring
- *fmt/stat/dyn* = format/static/dynamic oracle taxonomy of Luo et al.
- *TFS / TEFS* = Liu et al.'s Task Final Score and Task Execution Final Score
- *acc + OR* = accuracy plus Overhead Ratio
- *exec det* = deterministic execution-based oracles (this work)

| Paper           | Substrate          | Tools | LLMs | Oracle           | Federation   | L/M  |
|-----------------|--------------------|-------|------|------------------|--------------|------|
| MCP-Bench       | 28 servers, 11 dom | 250   | 20   | rule+rubric LLM  | none         | live |
| MCP-Universe    | 11 servers, 6 dom  | 133   | 16   | fmt/stat/dyn     | none         | live |
| MCPGauge        | 30 suites          | var   | 6    | acc + OR         | none         | live |
| Liu MCPAgentB.  | 9714 servers       | 20k+  | 11   | TFS / TEFS       | none         | mock |
| Guo MCP-AgentB. | 33 servers         | 188   | 10   | Sonnet 3.7 judge | none         | live |
| **Ours**        | **1 MCP, fed**     | **16**| **7**| **exec det**     | **cross-admin** | **live** |

LaTeX source (for direct re-insertion into FGCS):

```latex
\begin{table}[!htbp]
\centering\scriptsize
\caption{Positioning of this paper against the 2025 MCP-benchmark wave. Columns: substrate covered, tool count, LLMs evaluated, oracle methodology, federation scope, and L/M = whether servers are live or LLM-generated mocks. Shorthand: \emph{rule+rubric LLM} = rule-based plus rubric-driven LLM-as-judge scoring; \emph{fmt/stat/dyn} = format/static/dynamic oracle taxonomy of Luo et al.; \emph{TFS / TEFS} = Liu et al.'s Task Final Score and Task Execution Final Score; \emph{acc + OR} = accuracy plus Overhead Ratio; \emph{exec det} = deterministic execution-based oracles (this work).}
\label{tab:posA}
\begin{tabular}{@{}p{2.6cm}p{2.4cm}cccp{2.0cm}c@{}}
\toprule
\textbf{Paper} & \textbf{Substrate} & \textbf{Tools} & \textbf{LLMs} & \textbf{Oracle} & \textbf{Federation} & \textbf{L/M} \\
\midrule
MCP-Bench       & 28 servers, 11 dom & 250  & 20 & rule+rubric LLM & none      & live \\
MCP-Universe    & 11 servers, 6 dom  & 133  & 16 & fmt/stat/dyn    & none      & live \\
MCPGauge        & 30 suites          & var  & 6  & acc + OR        & none      & live \\
Liu MCPAgentB.  & 9714 servers       & 20k+ & 11 & TFS / TEFS      & none      & mock \\
Guo MCP-AgentB. & 33 servers         & 188  & 10 & Sonnet 3.7 judge & none     & live \\
\textbf{Ours}   & \textbf{1 MCP, fed}& \textbf{16} & \textbf{7} & \textbf{exec det} & \textbf{cross-admin} & \textbf{live} \\
\bottomrule
\end{tabular}
\end{table}
```

## Table 2 — Positioning vs scientific-data-MCP cluster

| Paper           | Substrate                       | Federation     | Evaluation             | Contribution           |
|-----------------|---------------------------------|----------------|------------------------|------------------------|
| Pan & Chard 2025| 7 MCP/Globus + ALCF/NERSC       | single domain  | 4 cases + Recall@k     | position + impls       |
| Kamatar 2025    | actor-model/Globus, no MCP      | single domain  | microbench + 4 cases   | fed-compute m/w        |
| **Ours**        | **16-tool MCP/Onedata**         | **cross-admin**| **7 LLMs × 18 tasks × K=8** | **curated MCP + bench** |

LaTeX source:

```latex
\begin{table}[!htbp]
\centering\tiny
\caption{Positioning B: vs scientific-data-MCP cluster.}
\label{tab:posB}
\begin{tabular}{@{}p{2.0cm}p{3.6cm}p{1.6cm}p{2.4cm}p{1.9cm}@{}}
\toprule
\textbf{Paper} & \textbf{Substrate} & \textbf{Federation} & \textbf{Evaluation} & \textbf{Contribution} \\
\midrule
Pan \& Chard 2025 & 7 MCP/Globus + ALCF/NERSC & single domain & 4 cases + Recall@k & position + impls \\
Kamatar 2025 & actor-model/Globus, no MCP & single domain & microbench + 4 cases & fed-compute m/w \\
\textbf{Ours} & \textbf{16-tool MCP/Onedata} & \textbf{cross-admin} & \textbf{7 LLMs} $\times$ \textbf{18 tasks} $\times$ \textbf{K=8} & \textbf{curated MCP + bench} \\
\bottomrule
\end{tabular}
\end{table}
```
