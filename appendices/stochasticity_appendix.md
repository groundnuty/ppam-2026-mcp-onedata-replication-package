# Stochasticity and tool-removal feasibility

*Cut from §5 Results. The main paper retains a single-sentence reference to the cell-stability decomposition; this appendix carries the full Table 9 + tool-removal feasibility argument.*

## Cell-stability decomposition + Tool-removal feasibility (LaTeX-source verbatim)

```latex
\subsection{Stable, stochastic, and stable-fail cells}
\label{sec:stochastic}
Reliability decomposes by cell-stability. Two LLMs (Sonnet, Gemma-4) have all eighteen cells stable-PASS. The next tier --- DeepSeek-V4-Pro, Qwen3.6, Devstral, GLM, Granite --- has between eleven and sixteen stable-PASS cells, with the remainder stochastic. Only one cell across the entire 126-pair grid is stable-FAIL: Granite~P1, where the agent calls \texttt{get\_file\_distribution} successfully but does not connect the canonical provider-name strings \texttt{cloud-pl} and \texttt{Cloud-SK} with their EU geography, so the answer reports the per-provider distribution correctly but never names the file as fully replicated to EU providers. This is a model-knowledge gap, not a tool-surface gap, and is dissected in Section~\ref{sec:findings}. The decomposition (Table~\ref{tab:stochastic}) makes the K=8 stochasticity visible at cell granularity --- a single $\mathrm{pass}^8$ aggregate would mix cells that flip 7-of-8 with cells that flip 3-of-8.

\begin{table}[!htbp]
\centering\small
\caption{Cell-stability decomposition at K=8 per panel LLM. Stable-PASS = all 8 trials pass; stable-FAIL = all 8 trials fail; stochastic = mixed.}
\label{tab:stochastic}
\begin{tabular}{@{}lccc@{}}
\toprule
\textbf{Model} & \textbf{Stable-PASS} & \textbf{Stable-FAIL} & \textbf{Stochastic} \\
\midrule
Claude Sonnet 4.5         & 18 / 18 & 0       & 0 \\
Google Gemma-4-31B-it     & 18 / 18 & 0       & 0 \\
DeepSeek-V4-Pro           & 16 / 18 & 0       & 2 \\
Alibaba Qwen3.6-35B       & 16 / 18 & 0       & 2 \\
Mistral Devstral-2-123B   & 12 / 18 & 0       & 6 \\
Z.ai GLM-4.7-Flash        & 11 / 18 & 0       & 7 \\
IBM Granite-4.1-30B       & 11 / 18 & 1 (P1)  & 6 \\
\bottomrule
\end{tabular}
\end{table}

\subsection{Tool-removal feasibility}
\label{sec:ablation}
The curation choices of Section~\ref{sec:background} are also testable in the negative direction --- which scenarios remain structurally feasible if the federation-state tools are removed and only file CRUD remains? Of the 18 scenarios, only four are feasible on a CRUD-only surface: Discovery $K_D = 1$ of 6 (D6 sanity, listing space names), Multi-step access $K_A = 3$ of 6 (A1, A2, A3 --- the metadata-tagging scenarios reachable through file-by-file metadata writes), Placement-introspection $K_P = 0$ of 6 (no agent can reason about replica geography, QoS rules, or transfer history from file CRUD alone). The $K$-counts are pre-registered: they follow deterministically from the tool-availability constraints and do not depend on which model runs. The full empirical K=8 sweep on the CRUD-only subset, against the headline 16-tool surface, is left to future work where the four-feasible-task budget can be paired with a higher $K$ for meaningful statistical power.

% ============================================================
```
