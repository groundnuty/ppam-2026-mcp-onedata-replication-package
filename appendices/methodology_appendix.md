# Methodology appendix

*Cut from §4 Methodology. The main paper retains the two-axis OracleResult + panel composition + lift policy + pass^k formula; this appendix carries the federation-reset 5-phase protocol detail, per-LLM-space architecture detail, oracle-taxonomy detail, reliability-metric detail, and the metrics + oracle-classification reference tables (Tables 5 and 6 in the original draft).*

## Tables 5 (Metrics summary) and 6 (Oracle classification) (LaTeX-source verbatim)

```latex
\begin{table}[!htbp]
\centering\small
\caption{Metrics summary (Table 2). Symbol / Unit / Formula / Source / Reported in.}
\label{tab:metrics}
\begin{tabular}{@{}llp{4.2cm}lp{1.8cm}@{}}
\toprule
\textbf{Sym} & \textbf{Unit} & \textbf{Formula} & \textbf{Source} & \textbf{In} \\
\midrule
$\mathrm{pass}^k$ & prop. & $\mathbb{E}_{\text{task}}[C(c,k)/C(n,k)]$ & Yao 2024 & T 5 \\
TC & calls & mean over trials & own & T 5 \\
WT & s & mean over trials & own & T 5 \\
$T_{in}$ & tok & mean over trials & Song 2025 & T 5 \\
$T_{out}$ & tok & mean over trials & Song 2025 & T 5 \\
$H_{name}$ & prop. & $|\{c: name \notin T\}|/N$ & Yao 2024 & T 7 \\
$H_{param}$ & prop. & $|\{c: params\,bad\}| / |\{name\,ok\}|$ & Yao 2024 & T 7 \\
$H_{qos}$ & prop. & $|\{INVALID\_QOS\_EXPR\}|/N_P$ & own & T 7 \\
TCS & tok/win & $(T_{in}+T_{out})/\mathrm{pass}^1$ & Liu 2025 inv. & T 5 \\
\bottomrule
\end{tabular}
\end{table}

% Table 3: Per-task oracle classification (18 rows; was Table 5 in plan)
\begin{table}[!htbp]
\centering\scriptsize
\caption{Per-task oracle classification (Table 3). 8 format / 8 static / 2 dynamic.}
\label{tab:oracles}
\begin{tabular}{@{}llp{6.0cm}l@{}}
\toprule
\textbf{ID} & \textbf{Band} & \textbf{Brief} & \textbf{Tier} \\
\midrule
D1 & Disc & List spaces with provider counts & format \\
D2 & Disc & Find files matching pattern in space X & format \\
D3 & Disc & Read file content and report size & format \\
D4 & Disc & List providers supporting space X & format \\
D5 & Disc & Get JSON metadata for path P & format \\
D6 & Disc & Trivial: list spaces (sanity check) & format \\
A1 & Acc  & Tag every raw file under /datasets/X & format \\
A2 & Acc  & Set reviewed=false on subtree & static \\
A3 & Acc  & Rename then re-tag a file & static \\
A4 & Acc  & Cross-space copy with metadata & static \\
A5 & Acc  & Multi-step QoS-aware write & static \\
A6 & Acc  & Recursive metadata predicate query & format \\
P1 & Plac & Where stored, fully replicated? & static \\
P2 & Plac & QoS-violation diagnostic & static \\
P3 & Plac & Force >=2 EU replicas (polling) & dynamic \\
P4 & Plac & Most-recent migration of file F & dynamic \\
P5 & Plac & QoS conflict resolution & static \\
P6 & Plac & replica\_count == 1 introspection & static \\
\bottomrule
\end{tabular}
\end{table}
```
