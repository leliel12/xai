---
title: "XAI Lecture 24 --- Explaining LLM Reasoning"
subtitle: "Background, then two papers: generate the reasoning (Rajani 2019) and localise the evidence (Yin \\& Neubig 2022)"
bibliography: references.bib
---

# Disclaimer

```{=latex}
\input{../disclaimer.tex}
```

# Background - the problem before the two papers {.shrink}

```{=latex}
Both papers attack the same question --- \textbf{how do large language models (LLMs, or LMs) reason?} --- from opposite ends. Before that, three things must be on the table:
\vskip2pt
\begin{center}
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig00.pdf}
\end{center}
\vskip2pt
\begin{itemize}
  \item \textbf{What commonsense reasoning is}, and why it is hard for NLU (Natural Language Understanding) systems.
  \item \textbf{ConceptNet} --- the knowledge graph (Speer et al., 2017).
  \item \textbf{CommonsenseQA (CQA)} --- the question-answering (QA) benchmark built \emph{from} ConceptNet (Talmor et al., 2019); the lecture's shared backdrop, and the benchmark \textbf{Paper 1} builds on.
\end{itemize}
```

# Why commonsense is hard for NLU

```{=latex}
\begin{columns}[T]
\begin{column}{0.512\textwidth}
When people answer, they draw on world knowledge \textbf{not present in the text}: space, cause and effect, social conventions.\\[2pt]
Example (Talmor et al., 2019):\\
\emph{``Where was Simon when he heard the lawn mower?''}\\
A human silently infers: a lawn mower is \textbf{outdoors}, at \textbf{street level} $\to$ Simon was \textbf{outside}.
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.418\textwidth}
\begin{block}{The contrast}
Classic reading-comprehension QA --- e.g. \textbf{SQuAD} (\emph{Stanford Question Answering Dataset}, Rajpurkar et al.\ 2016: questions whose answer is a \emph{span of text} inside a given paragraph) --- hands you that paragraph and asks about \emph{it}. Commonsense QA gives \textbf{no} paragraph: the knowledge must already be in the model.
\end{block}
\begin{block}{Remarks}
``Trivial for humans, out of reach for NLU systems'' --- the gap this whole lecture circles around.
\end{block}
\end{column}
\end{columns}
```

# ConceptNet: a commonsense knowledge graph (Speer et al., 2017)

```{=latex}
\begin{columns}[T]
\begin{column}{0.465\textwidth}
ConceptNet stores everyday knowledge as a \textbf{graph of triples}:
\begin{itemize}
  \item nodes are \textbf{concepts} (words / phrases);
  \item edges are \textbf{named relations}.
\end{itemize}
Typical relations: \texttt{IsA}, \texttt{AtLocation}, \texttt{UsedFor}, \texttt{CapableOf}, \texttt{Causes}, \texttt{PartOf}, \texttt{HasProperty}.
\begin{block}{Remarks}
Common sense \emph{written down} as a graph: millions of obvious facts, each a typed arrow between two ideas.
\end{block}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.465\textwidth}
\centering
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig01.pdf}\\[6pt]
{\scriptsize Other triples: \texttt{knife-UsedFor->cutting}, \texttt{bird-CapableOf->fly}.}
\end{column}
\end{columns}
```

# The benchmark paper: Talmor et al. (2019) {.shrink}

```{=latex}
\begin{columns}[T]
\begin{column}{0.522\textwidth}
From here until \textbf{the gap} (the large \textbf{model--human accuracy gap}), the Background follows \textbf{one paper}: \textbf{Talmor, Herzig, Lourie \& Berant (2019)} --- \emph{CommonsenseQA} (NAACL-HLT).
\begin{itemize}
  \item \textbf{Purpose:} build a test that \textbf{forces common sense} --- a model passes \textbf{only} by using \textbf{everyday world knowledge it already has}.
  \begin{itemize}\footnotesize
    \item \textbf{No passage to look up} $\to$ the answer is \emph{not} written in the text.
    \item \textbf{No word-matching shortcut} $\to$ you cannot win by picking the option that just ``sounds related''.
  \end{itemize}
  \item \textbf{Key idea:} build every question from \textbf{ConceptNet} so all answer choices are \textbf{close siblings} (\textbf{same source concept, same relation}).
  \begin{itemize}\footnotesize
    \item They all ``sound related'' \textbf{equally} $\to$ word-matching \textbf{fails}.
    \item \textbf{Only reading the specific situation} picks the right one.
  \end{itemize}
\end{itemize}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.408\textwidth}
\begin{block}{Where it sits}
\textbf{ConceptNet} (Speer 2017) is the \emph{ingredient}; \textbf{CommonsenseQA} (Talmor 2019) is the \emph{benchmark} built from it. The two focal papers come \emph{after}.
\end{block}
\begin{block}{Example --- why matching fails}
\footnotesize From \textbf{river} (\texttt{AtLocation}): \textbf{waterfall, bridge, valley} --- all ``sound related'' to \emph{river} \textbf{equally}. Only the \textbf{specific situation} (``hold a cup upright to catch water'') points to \textbf{waterfall}.
\end{block}
\end{column}
\end{columns}
```

# From ConceptNet to questions: how CQA is built

```{=latex}
\begin{columns}[T]
\begin{column}{0.493\textwidth}
{\scriptsize a) Sample ConceptNet for specific subgraphs}\\[2pt]
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig02.pdf}\\[3pt]
{\scriptsize b) Crowd-source questions + two distractors}\\[2pt]
{\tiny\emph{Where on a \textbf{river} can you hold a cup upright to catch water on a sunny day?}\\
\textcolor[HTML]{2E7D32}{\ensuremath{\checkmark}}\,\textbf{waterfall}, \textcolor[HTML]{2E5FA3}{bridge}, \textcolor[HTML]{2E5FA3}{valley}, \textcolor[HTML]{C0392B}{pebble}, \textcolor[HTML]{7D3C98}{mountain}\\[2pt]
\emph{Where can I stand on a \textbf{river} to see water falling without getting wet?}\\
\textcolor[HTML]{2E5FA3}{waterfall}, \textcolor[HTML]{2E7D32}{\ensuremath{\checkmark}}\,\textbf{bridge}, \textcolor[HTML]{2E5FA3}{valley}, \textcolor[HTML]{C0392B}{stream}, \textcolor[HTML]{7D3C98}{bottom}\\[2pt]
\emph{I'm crossing the \textbf{river}, my feet are wet but my body is dry, where am I?}\\
\textcolor[HTML]{2E5FA3}{waterfall}, \textcolor[HTML]{2E5FA3}{bridge}, \textcolor[HTML]{2E7D32}{\ensuremath{\checkmark}}\,\textbf{valley}, \textcolor[HTML]{C0392B}{bank}, \textcolor[HTML]{7D3C98}{island}\par}
{\scriptsize Talmor et al.\ (2019), Fig.\ 1.}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.437\textwidth}
{An \textbf{Amazon Mechanical Turk (MTurk)} crowdworker {\bfseries\color{red}HG1}, the question-writers --- paid humans who \emph{write data} --- see a \textbf{source concept} (\emph{river}, green) and \textbf{three target concepts} (\emph{waterfall, bridge, valley}, blue) sharing one relation (\texttt{AtLocation}).\\[3pt]
{\bfseries\color{red}HG1}, the question-writers (Talmor et al.\ 2019) writes \textbf{three questions}, one per target as the answer; the other two are distractors.\\[3pt]
Then per question: \textbf{+1 ConceptNet distractor} (\textcolor[HTML]{C0392B}{red}) and \textbf{+1 hand-authored} (\textcolor[HTML]{7D3C98}{purple}) $\to$ \textbf{5 choices} total.\par}
\end{column}
\end{columns}
```

# Why the distractors make it hard

```{=latex}
\begin{columns}[T]
\begin{column}{0.484\textwidth}
The distractors are \textbf{ConceptNet siblings}: same source concept, same relation.\\[6pt]
\centering
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig03.pdf}\\[6pt]
So a model \textbf{cannot} win by spotting which option ``sounds river-ish'' --- they all do. It must read the specific situation (\emph{hold a cup upright to catch water}).
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.446\textwidth}
\begin{block}{Key insight}
The construction \emph{forces} commonsense: surface word-association is neutralised by design, because every option is equally associated with the source.
\end{block}
\end{column}
\end{columns}
```

# Talmor's experiment, and the gap {.shrink}

```{=latex}
\begin{columns}[T]
\begin{column}{0.397\textwidth}
\vspace*{-7mm}
\begin{block}{The model--human gap}
\centering
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig04.pdf}

{\scriptsize Gap $=$ 89\% $-$ 56\% $=$ \textbf{33 pts}}
\end{block}
\begin{block}{The Google-snippets test}
\textbf{Source:} top results from a \textbf{web search engine} (Google), retrieved per question.\\[1pt]
\textbf{Selection:} query the question; keep the \textbf{highest-ranked snippets}.\\[1pt]
\textbf{Given to:} \textbf{BIDAF++}, a \textbf{reading-comprehension} (RC) model that reads the snippets \emph{as a passage} and then answers.\\[1pt]
\textbf{Result:} \textbf{no gain} --- the RC baseline reaches only $\approx$47.7\% (below GPT's 54.8\% with no passage) $\to$ extra text does \textbf{not} close the gap; the limit is \textbf{reasoning}.
\end{block}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.533\textwidth}
{\textbf{BERT as the evaluation baseline.}} {\footnotesize An examiner who designs an assessment, then administers it to a cohort of candidates:}
\begin{enumerate}\footnotesize\setlength\itemsep{0pt}
  \item \textbf{Construction of the instrument.} Talmor's contribution is the benchmark itself ($\sim$12,247 CommonsenseQA items) --- the \emph{dataset}, not any model.
  \item \textbf{Administration to candidate models.} The benchmark is administered to several 2019 systems; among them \textbf{BERT-large}, the rest weaker or of earlier design.
  \item \textbf{Evaluation protocol.} Each model is fine-tuned on the training partition, then assessed on the held-out test partition (standard supervised evaluation).
  \item \textbf{Comparison of results.} BERT-large attains the highest accuracy, yet only $\approx$56\%, against $\approx$89\% for the \textbf{human respondents} ({\bfseries\color{red}HG2}, the test-takers) --- not the writers.
\end{enumerate}
{\footnotesize\textbf{Interpretation:} if even the strongest contemporary model reaches only 56\%, models do not yet exhibit genuine commonsense reasoning --- the difficulty arises from \textbf{reasoning}, not from missing information.}
\end{column}
\end{columns}
```

# Talmor et al. (2019): the conclusion

```{=latex}
\begin{columns}[T]
\begin{column}{0.465\textwidth}
\textbf{Data obtained (measured)}
\begin{itemize}
  \item Best model \textbf{BERT-large} $\approx 56\%$ (5-choice test).
  \item \textbf{Humans} ({\bfseries\color{red}HG2}, the test-takers) $\approx 89\%$ on the same test.
  \item Adding \textbf{retrieved web snippets}: no accuracy gain.
\end{itemize}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.465\textwidth}
\textbf{Scientific conclusions}
\begin{itemize}
  \item The model--human gap is \textbf{large} $\to$ today's models \textbf{do not genuinely reason} with commonsense.
  \item Since extra text doesn't help, the limit is \textbf{reasoning with world knowledge, not missing data}.
  \item By construction the benchmark \textbf{isolates commonsense} $\to$ a sound, shared yardstick.
\end{itemize}
\end{column}
\end{columns}
\begin{block}{Why it matters for us}
The lecture's \textbf{shared backdrop}: \textbf{Paper 1 (Rajani)} is evaluated on it; the open question --- \emph{how} do these models reason? --- launches the two focal papers.
\end{block}
```

# Background $\to$ the two papers

```{=latex}
\vfill
\begin{block}{The set-up for the lecture}
Given this hard benchmark, two natural questions follow:
\end{block}
\begin{itemize}
  \item \textbf{Paper 1 (Rajani et al., 2019):} can a model that \emph{writes its reasoning} answer better? $\to$ \emph{generate} explanations.
  \item \textbf{Paper 2 (Yin \& Neubig, 2022):} can we \emph{point at the exact input word} that drove a prediction? $\to$ \emph{localise} evidence.
\end{itemize}
\begin{center}\textbf{verbalise the reasoning} \qquad vs. \qquad \textbf{localise the evidence}\end{center}
\vfill
```

# The two papers at a glance {.shrink}

```{=latex}
{\footnotesize Unit IV $\cdot$ Week 12. Both methods explain a trained \textbf{language model (LM)} \emph{from the outside} (inputs $\to$ outputs); the LM differs per paper. \emph{Two key terms:} \textbf{plausibility} $=$ looks convincing to a person; \textbf{faithfulness} ($=$ fidelity) $=$ reflects the model's \emph{true} computation.}
\begin{columns}[T]
\begin{column}{0.465\textwidth}
\begin{block}{Paper 1 $\cdot$ Rajani (2019) --- CAGE}
\footnotesize \emph{CAGE $=$ Commonsense Auto-Generated Explanations.}\\
\textbf{Verbalise the reasoning}
\begin{itemize}\footnotesize
  \item \textbf{Explains:} a BERT classifier (few options).
  \item \textbf{What:} a natural-language narrative.
  \item \textbf{Goal:} plausibility + accuracy.
  \item \textbf{Family:} generated explanation.
  \item \textbf{Nature:} black-box (internals not seen).
\end{itemize}
\end{block}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.465\textwidth}
\begin{block}{Paper 2 $\cdot$ Yin \& Neubig (2022)}
\footnotesize \textbf{Localise the evidence}
\begin{itemize}\footnotesize
  \item \textbf{Explains:} a generative LM --- GPT-2 / GPT-Neo ($\sim$50k tokens).
  \item \textbf{What:} attributes output to input tokens.
  \item \textbf{Goal:} faithfulness, fine-grained evidence.
  \item \textbf{Family:} contrastive attribution (grad./erasure).
  \item \textbf{Nature:} gradients (white-box) or perturbation.
\end{itemize}
\end{block}
\end{column}
\end{columns}
\begin{center}\includegraphics[width=0.6\linewidth,height=0.16\textheight,keepaspectratio]{imgs/fig17.pdf}\end{center}
```

# Paper 1

```{=latex}
\begin{center}
\includegraphics[width=0.92\columnwidth]{imgs/paper1_title.png}
\end{center}
```

# What is CAGE?

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries FRAMING}}\par}\vspace{0.6mm}
\begin{columns}[T]
\begin{column}{0.522\textwidth}
\textbf{CAGE $=$ Commonsense Auto-Generated Explanations.}\\
A framework that uses a \textbf{language model to} \emph{automatically generate} a natural-language \textbf{explanation} for each question, then feeds that explanation as \textbf{extra input} to a classifier that picks the answer.
\begin{itemize}
  \item \textbf{Origin / authorship:} introduced by \textbf{Rajani, McCann, Xiong \& Socher (Salesforce Research)} in \emph{Explain Yourself!}, \textbf{ACL 2019}.
  \item \textbf{Why it mattered:} the first method to \emph{generate} reasoning text and use it to \textbf{improve} commonsense QA --- it raised the \textbf{state of the art on CommonsenseQA v1.0 by} $\approx 10\%$.
\end{itemize}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.408\textwidth}
\begin{block}{A note on terminology}
\footnotesize
Two terms recur and must be kept distinct. \textbf{CoS-E} names the \emph{resource} --- the dataset of human explanations (free-form rationales and highlighted spans). \textbf{CAGE} names the \emph{method} --- a language model, fine-tuned on CoS-E, that \emph{produces} the rationale subsequently supplied to the answer classifier.\\[3pt]
The acronym's expansion is not standardised: the paper's title uses the singular (\emph{Commonsense Auto-Generated Explanation}), whereas the literature favours the plural (\emph{Explanations}), adopted here.\\[3pt]
\textbf{Convention for these slides:} \textbf{CoS-E} always denotes the data, \textbf{CAGE} the model.
\end{block}
\end{column}
\end{columns}
```

# Introduction + Motivation

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries FRAMING}}\par}\vspace{0.6mm}
\begin{columns}[T]
\begin{column}{0.512\textwidth}
\textbf{The problem:} deep models do poorly on tasks needing commonsense reasoning --- knowledge not present in the input.
\begin{itemize}
  \item An \textbf{explanation} verbalises the reasoning a model uses.
  \item \textbf{CommonsenseQA} is the benchmark (Talmor et al., 2019) --- see Background.
  \item Open question: \emph{do} these models reason, and how much rests on world knowledge?
\end{itemize}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.418\textwidth}
\begin{block}{Core idea}
Train a language model to \textbf{generate explanations}, feed them to a classifier, and \textbf{measure whether they help}.
\end{block}
\begin{block}{Remarks}
Test whether the explanation \emph{helps accuracy} --- not merely whether it sounds nice.
\end{block}
\end{column}
\end{columns}
```

# The pipeline, end to end

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries METHOD --- OVERVIEW}}\par}\vspace{0.6mm}
\begin{columns}[T]
\begin{column}{0.512\textwidth}
\begin{enumerate}
  \item Take a CQA example: question $q$, choices $c_0,c_1,c_2$, gold answer $a$ (the ground-truth answer).
  \item A \textbf{different} crowd of humans ({\bfseries\color{red}HG3}, the explanation-writers; MTurk, \emph{not} {\bfseries\color{red}HG1}) writes explanation $e_h$ for \emph{why} $a$ is correct (this is \textbf{CoS-E} --- Common Sense Explanations).
  \item Fine-tune a language model (\textbf{GPT} --- generative pre-training, Radford et al., 2018) to generate $e\approx e_h$.
  \item Concatenate $q+$choices$+\,e$ and feed to a \textbf{BERT} classifier (bidirectional encoder, Devlin et al., 2019).
  \item Measure: does $e$ raise accuracy?
\end{enumerate}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.418\textwidth}
\centering
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig05.pdf}\\[4pt]
\begin{block}{Remarks}
\textbf{GPT explains, BERT decides.} The LM is a \emph{commentator}, not the \emph{judge} --- roles unpacked next slide.
\end{block}
\end{column}
\end{columns}
```

# Two models, two roles: GPT narrates, BERT rules {.shrink}

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries METHOD --- OVERVIEW}}\par}\vspace{0.6mm}
\begin{columns}[T]
\begin{column}{0.474\textwidth}
A \textbf{legal metaphor} for CAGE's two-model design:
\begin{itemize}
  \item \textbf{GPT --- the commentator.} The \emph{generator}. Given $q+$choices it \textbf{writes} the explanation $e$; it never selects the answer. It narrates \emph{why} an option could make sense.
  \item \textbf{BERT --- the judge.} The \emph{classifier}. It reads $q+$choices$+\,e$ and \textbf{delivers the verdict} --- the predicted answer that is actually scored.
\end{itemize}
A commentator can give a compelling account, but only the judge's ruling counts. This is exactly why CAGE measures \textbf{plausibility}, not fidelity: the narrator's story need not be what the judge truly used to decide.
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.456\textwidth}
\begin{block}{The two architectures of the era (2018--19)}
\footnotesize
\textbf{OpenAI GPT} (GPT-1; Radford et al.\ 2018): a \emph{decoder-only} Transformer (Vaswani et al., 2017), \emph{left-to-right} (autoregressive), $\sim$117M params, pre-trained on BooksCorpus. Built to \textbf{generate} text.\\[4pt]
\textbf{BERT} (Devlin et al.\ 2019): an \emph{encoder-only} Transformer, \emph{bidirectional} (masked-language-model pre-training); fine-tuned for multiple choice by a classifier on the \texttt{[CLS]} (classification) token. Base 110M / large 340M. Built to \textbf{understand / score}.
\end{block}
\end{column}
\end{columns}
```

# Didactic aside: what does "fine-tune GPT to generate $e$" mean?

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries METHOD --- OVERVIEW}}\par}\vspace{0.6mm}
\vskip1em
\begin{columns}[T]
\begin{column}{0.512\textwidth}
\begin{itemize}
  \item A language model already knows how to \textbf{continue text} (next-word prediction).
  \item \textbf{Fine-tuning} $=$ keep training it, but now on \texttt{(question + choices) --> explanation} pairs from CoS-E.
  \item After fine-tuning, given a \emph{new} question it can \textbf{write its own explanation}, imitating the human ones.
\end{itemize}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.418\textwidth}
\begin{block}{Mental model}
We are not teaching new facts; we are teaching a \emph{format}: ``given a question, produce the kind of one-sentence justification a person would write.''
\end{block}
\end{column}
\end{columns}
```

# How CoS-E is built from CQA --- step by step {.shrink}

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries DATASET --- CoS-E (HUMANS)}}\par}\vspace{0.6mm}
\begin{columns}[T]
\begin{column}{0.556\textwidth}
\begin{enumerate}\setlength\itemsep{1.5pt}
  \item \textbf{Start from CQA.} Take CommonsenseQA (\textbf{Talmor et al.\ 2019}) --- the questions {\bfseries\color{red}HG1} built from ConceptNet.
  \item \textbf{Pick one item.} A question together with its \textbf{correct (gold) answer}.
  \item \textbf{Give it to {\bfseries\color{red}HG3}.} A \textbf{different} MTurk crowd reads that question $+$ gold answer.
  \item \textbf{Highlight.} They mark the words that justify the answer $\to$ \textbf{CoS-E-selected}.
  \item \textbf{Explain.} They write \textbf{one sentence} saying \emph{why} the answer is right $\to$ \textbf{CoS-E-open-ended}.
  \item \textbf{Filter \& collect.} Drop bad ones; now every CQA item carries a human explanation $=$ \textbf{CoS-E}.
\end{enumerate}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.374\textwidth}
\centering
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig06.pdf}\\[2mm]
{\scriptsize \textbf{Sizes (train / dev):} v1.0 $=$ 7,610 / 950; v1.11 $=$ 9,741 / 1,221.}\\[2mm]
\begin{block}{Why it matters}
CoS-E is the \textbf{supervision} for \textbf{CAGE} (next): the \textbf{only} thing humans write here --- from now on, the \textbf{model} writes the explanations.
\end{block}
\end{column}
\end{columns}
```

# CoS-E: three concrete examples (Table 1)

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries DATASET --- CoS-E (HUMANS)}}\par}\vspace{0.6mm}
\vskip0.5em
\begin{block}{From the paper}
\textbf{Q:} ``While eating a hamburger with friends, what are people trying to do?'' \quad Choices: \textbf{have fun}, tasty, indigestion.\\
\textbf{CoS-E:} ``Usually a hamburger with friends indicates a good time.''
\end{block}
\begin{block}{}
\textbf{Q:} ``After getting drunk people couldn't understand him, it was because of his what?'' \quad Choices: lower standards, \textbf{slurred speech}, falling down.\\
\textbf{CoS-E:} ``People who are drunk have difficulty speaking.''
\end{block}
\begin{block}{}
\textbf{Q:} ``People do what during their time off from work?'' \quad Choices: \textbf{take trips}, brow shorter, become hysterical.\\
\textbf{CoS-E:} ``People usually do something relaxing, such as taking trips, when they don't need to work.''
\end{block}
```

# CoS-E: a worked example, link by link

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries DATASET --- CoS-E (HUMANS)}}\par}\vspace{0.6mm}
\vskip0.5em
\begin{columns}[T]
\begin{column}{0.522\textwidth}
\begin{block}{Question}
While eating a hamburger with friends, what are people trying to do? --- \textbf{have fun} / tasty / indigestion.
\end{block}
The bare question never says friends are enjoyable. The explanation supplies the missing world-knowledge link:\\[4pt]
\centering hamburger $+$ friends $\to$ social $\to$ enjoyable $\to$ \textbf{have fun}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.408\textwidth}
\begin{block}{Remarks}
That chain --- ``hamburger with friends $=$ a good time'' --- is commonsense made \emph{explicit in words}. That is what CoS-E records.
\end{block}
\end{column}
\end{columns}
```

# What is "CSRM" --- and why is BERT the one doing it?

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries METHOD --- CAGE (ALGORITHM)}}\par}\vspace{0.6mm}
\begin{columns}[T]
\begin{column}{0.512\textwidth}
\begin{enumerate}\setlength\itemsep{3pt}
  \item \textbf{CSRM $=$ Commonsense Reasoning Model.} It is a \textbf{role}: ``the model that decides the answer''.
  \item That role is a \textbf{classification} job --- read $q +$ choices $+$ explanation, then \textbf{pick one} option.
  \item They fill the role with \textbf{BERT}, because BERT is a \textbf{classifier} (built to read and score). GPT \emph{cannot} do this --- it only \textbf{generates} text.
  \item So ``\textbf{CSRM (BERT)}'' just names \textbf{who fills the role} --- like writing ``the judge (Dr.\ P\'erez)''.
\end{enumerate}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.418\textwidth}
\begin{block}{Why BERT \emph{can} pick an answer}
BERT is \textbf{fine-tuned} for multiple choice by adding a tiny classifier on its special \texttt{[CLS]} token --- that is what lets it \textbf{choose} one of the options.
\end{block}
\begin{block}{Memory hook}
\textbf{GPT narrates, BERT judges.} The narrator writes the story; only the judge's ruling counts.
\end{block}
\end{column}
\end{columns}
```

# The two phases: GPT writes the "why", BERT picks the answer

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries METHOD --- CAGE (ALGORITHM)}}\par}\vspace{0.6mm}
\begin{columns}[T]
\begin{column}{0.474\textwidth}
\centering
{\scriptsize (a) generate --- GPT writes the explanation}\\[2pt]
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig07.pdf}\\[6pt]
{\scriptsize (b) classify --- BERT picks the answer}\\[2pt]
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig08.pdf}\\[8pt]
\begin{block}{Reading the symbols}
\scriptsize
$q$ = the question; \quad $c_0 c_1 c_2$ = the 3 answer choices.\\[1pt]
$E_0\dots E_n$ = the explanation, written \textbf{token by token} (a \textbf{token} $\approx$ a word).\\[1pt]
$E_i$ = the \textbf{next} word being written; \quad $A$ = the chosen answer.
\end{block}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.456\textwidth}
\begin{enumerate}\setlength\itemsep{4pt}
  \item \textbf{Phase (a) --- generate.} GPT reads $q +$ choices $+$ \textbf{the words written so far} ($E_0\dots E_{i-1}$) and writes \textbf{the next word} $E_i$. Repeat until the explanation is finished.
  \item \textbf{Phase (b) --- classify.} The other model reads $q +$ choices $+$ \textbf{the finished explanation} ($E_0\dots E_n$) and picks the answer $A$.
\end{enumerate}
\begin{block}{Half-written vs.\ finished}
In (a) the explanation is \textbf{half-written} (up to $E_{i-1}$); in (b) it is \textbf{complete} (up to $E_n$).
\end{block}
\end{column}
\end{columns}
```

# How GPT learns to explain --- the training loop

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries METHOD --- CAGE (ALGORITHM)}}\par}\vspace{0.6mm}
\vskip1pt
\begin{center}
\resizebox{0.80\textwidth}{!}{%
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig09.pdf}}
\end{center}
\vskip2pt
{\footnotesize \textbf{Read it:} the prompt enters the network; the prediction is \textbf{compared} with the human explanation (\textbf{CoS-E}, the target); the \textbf{error} flows back (backprop) to adjust the weights. CoS-E never enters as input --- it is the \textbf{reference for correction}.}
```

# Where the prompts come from --- and what CoS-E is

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries METHOD --- CAGE (ALGORITHM)}}\par}\vspace{0.6mm}
\vskip2pt
{\footnotesize \textbf{Where do the prompts come from?} $C_{RE}$ is \textbf{not a stored dataset} --- it is the \textbf{template} (the ``mould'') of the reasoning prompt. There is \textbf{one $C_{RE}$ per CQA question} (one per item), \textbf{assembled on the fly} from $q +$ choices $+$ a fixed template. No prompt is written by hand.}
\vskip3pt
\begin{block}{Example prompt --- reasoning ($C_{RE}$): answer \emph{absent}}
``\dots have fun, tasty, or indigestion? \emph{commonsense says} \rule{1.6cm}{0.4pt}''
\end{block}
\begin{block}{Example prompt --- rationalisation ($C_{RA}$): answer \emph{present}}
``\dots have fun, tasty, or indigestion? \textbf{have fun} \emph{because} \rule{1.6cm}{0.4pt}''
\end{block}
\begin{block}{Example CoS-E explanation --- the \emph{target} GPT must learn}
``a hamburger with friends usually means a good time''
\end{block}
\begin{center}\textbf{The whole game: is the answer in the prompt or not?} $C_{RE}$ \textbf{reasons} (before the answer); $C_{RA}$ \textbf{justifies} after the fact.\end{center}
```

# Results: CommonsenseQA v1.0 (3 choices)

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries EVALUATION --- RESULTS}}\par}\vspace{0.6mm}
\vskip2pt
\begin{columns}[T]
\begin{column}{0.4\textwidth}
\textbf{Dev (random split)}\\[2pt]
{\footnotesize\begin{tabular}{@{}lr@{}}
\hline
Method & Acc.\ (\%)\\
\hline
BERT baseline & 63.8\\
CoS-E-open-ended & 65.5\\
CAGE-reasoning & \textbf{72.6}\\
\hline
\end{tabular}}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.42\textwidth}
\textbf{Test split (v1.0)}\\[2pt]
{\footnotesize\begin{tabular}{@{}lr@{}}
\hline
Method & Acc.\ (\%)\\
\hline
RC (Talmor) & 47.7\\
GPT (Talmor) & 54.8\\
CoS-E-open-ended & 60.2\\
CAGE-reasoning & \textbf{64.7}\\
Human ({\bfseries\color{red}HG2}) & 95.3\\
\hline
\end{tabular}}
\end{column}
\end{columns}
\vskip3pt
\begin{block}{Reading the tables --- what each term means}
\scriptsize
\textbf{Accuracy (\%)} --- the share of multiple-choice questions answered correctly; the sole metric. \enspace
\textbf{Dev vs.\ Test split} --- the data is partitioned: the \emph{development} split is used while tuning, the \emph{test} split is held out for the final score. \enspace
\textbf{Baseline} --- a reference system without the proposed mechanism (BERT, no explanations). \enspace
\textbf{Human (HG2)} --- the \emph{human ceiling}: accuracy attained by people. \enspace
\textbf{RC} $=$ reading-comprehension baseline (Talmor et al., 2019).
\end{block}
\begin{block}{Headline}
{\footnotesize CAGE-reasoning gains $\approx 10$ percentage points (\textbf{absolute}, i.e.\ $64.7-54.8$) over the previous \textbf{state of the art} on test --- yet stays far below the \textbf{human ceiling} of 95.3\%.}
\end{block}
```

# Results: CQA v1.11 (5 choices) --- the uncomfortable result

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries EVALUATION --- RESULTS}}\par}\vspace{0.6mm}
\vskip2pt
{\footnotesize \textbf{What is v1.11?} The \textbf{harder, 5-choice} edition of CommonsenseQA (v1.0 has 3 choices). The \emph{uncomfortable result}: here the proposed method \textbf{underperforms} the plain baseline.}
\vskip5pt
\begin{columns}[T]
\begin{column}{0.388\textwidth}
{\footnotesize\begin{tabular}{@{}lr@{}}
\hline
Method & Acc.\ (\%)\\
\hline
CAGE-reasoning & 55.7\\
BERT baseline & 56.7\\
\textbf{CoS-E-open-ended} & \textbf{58.2}\\
\hline
\end{tabular}}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.543\textwidth}
\begin{block}{Remarks}
On the harder 5-choice version \textbf{CAGE loses to the plain baseline} (55.7 vs.\ 56.7). The generated explanation often \emph{contains} the correct answer, yet the classifier cannot exploit it.
\end{block}
\end{column}
\end{columns}
\begin{block}{Connect to background --- why it is hard}
\textbf{ConceptNet siblings}: in v1.11 the five answer choices are all drawn from \emph{related} ConceptNet nodes, so they are \textbf{semantically very close}. With options that similar, merely \emph{concatenating} the explanation is not discriminative enough. The authors report this negative result \textbf{honestly}.
\end{block}
```

# Results: out-of-domain transfer

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries EVALUATION --- RESULTS}}\par}\vspace{0.6mm}
\vskip2pt
{\footnotesize \textbf{What is out-of-domain transfer?} Applying the explanation module \textbf{trained on CQA} to \textbf{different tasks}, with \textbf{no retraining}, to test whether the explanations still help.}
\vskip5pt
\begin{columns}[T]
\begin{column}{0.426\textwidth}
{\footnotesize\begin{tabular}{@{}lrr@{}}
\hline
Method & SWAG & Story Cloze\\
\hline
BERT & 84.2 & 89.8\\
$+$ explanation transfer & 83.6 & 89.5\\
\hline
\end{tabular}}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.504\textwidth}
\begin{block}{Remarks}
Transfer without retraining costs only a \textbf{tiny drop} ($<0.6\%$). The explanations are fluent and relevant, but give \textbf{no downstream gain} (\emph{downstream} $=$ on the end task). An \emph{honest negative result}.
\end{block}
\end{column}
\end{columns}
\begin{block}{Datasets --- the table columns}
\scriptsize
\textbf{SWAG} $=$ Situations With Adversarial Generations (Zellers et al., 2018) --- commonsense inference about everyday situations. \enspace
\textbf{Story Cloze} (Mostafazadeh et al., 2016) --- choosing the correct ending of a short story. \enspace Both are \textbf{separate} tasks from CommonsenseQA.
\end{block}
```

# Qualitative analysis --- what the explanations look like {.shrink}

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries ANALYSIS --- LIMITATIONS}}\par}\vspace{0.6mm}
\vskip2pt
\begin{columns}[T]
\begin{column}{0.512\textwidth}
\begin{itemize}\setlength\itemsep{3pt}
  \item \textbf{Form}: CAGE uses \textbf{simpler constructions} than the humans' ({\bfseries\color{red}HG3}), yet can be \emph{more} informative.
  \item \textbf{Content}: an answer choice appears \textbf{43\%} of the time, but the \emph{predicted} choice only \textbf{21\%} $\to$ the explanation is \textbf{not a circular echo} of the chosen answer.
  \item \textbf{Similarity to humans}: BLEU peaks at \textbf{4.1} (vs 0.8 untuned) --- \textbf{very low}. \quad \textbf{Fluency}: perplexity \textbf{32} --- low, i.e.\ fluent.
\end{itemize}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.418\textwidth}
\begin{block}{Foreshadowing}
Low BLEU but real usefulness $\to$ something can be \emph{useful without resembling human wording}. This previews Paper 2's \textbf{faithfulness vs.\ plausibility} tension.
\end{block}
\end{column}
\end{columns}
\vskip3pt
\begin{block}{The two metrics, defined}
\scriptsize
\textbf{BLEU} (Papineni et al., 2002) --- measures \textbf{how closely a generated text matches human reference texts} by counting shared word sequences (n-grams). Range 0--100; \textbf{higher $=$ more similar} to the references. The value 4.1 means CAGE's wording is \textbf{far} from the humans'. \\[2pt]
\textbf{Perplexity} --- measures \textbf{how fluent / predictable} a text is for a language model; formally the exponential of the average per-word cross-entropy. \textbf{Lower $=$ the model is less ``surprised'' $=$ more fluent text}. The value 32 indicates fluent output.
\end{block}
```

# Limitations + critical reading of Rajani et al.

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries ANALYSIS --- LIMITATIONS}}\par}\vspace{0.6mm}
\vskip1em
\begin{itemize}
  \item \textbf{Human simulatability is low:} from the explanation alone, {\bfseries\color{red}HG4a}, Rajani's Turkers (evaluators who \emph{judge}, not writers) recover the model's answer \textbf{42\%} (CAGE) vs \textbf{52\%} (human) $\to$ the explanation does not transparently reveal the model.
  \item \textbf{Adversarial explanations are catastrophic:} misleading explanations drop accuracy \textbf{60\%} $\to$ \textbf{30\%} --- below the 50\% baseline.
  \item \textbf{Bias propagation:} CQA gender disparity flows into CoS-E and the trained models.
\end{itemize}
```

# Bridge to Paper 2

```{=latex}
{\vspace{-3.4mm}\hfill\setlength{\fboxsep}{3pt}\colorbox[HTML]{B8860B}{\textcolor{white}{\scriptsize\bfseries TRANSITION --- TO PAPER 2}}\par}\vspace{0.6mm}
\vskip0.5em
\begin{columns}[T]
\begin{column}{0.512\textwidth}
\begin{block}{What Rajani et al.\ leave open}
They \emph{generate} explanations, but never ask: \textbf{which input tokens caused this prediction}, and \textbf{why this token instead of another?}
\end{block}
\begin{itemize}
  \item Generated explanations are \textbf{plausible} but not necessarily \textbf{faithful}.
  \item Language generation has an enormous output space $\to$ we need a \textbf{token-level} lens.
\end{itemize}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.418\textwidth}
\begin{block}{Next}
\textbf{Yin \& Neubig (2022):} contrastive input-\emph{saliency} (which input tokens mattered) --- look \emph{inside} the model.
\end{block}
\end{column}
\end{columns}
```

# Paper 2

```{=latex}
\begin{center}
\includegraphics[width=0.92\columnwidth]{imgs/paper2_title.png}
\end{center}
```

# Paper 2 in a nutshell: what it is about {.shrink}

```{=latex}
\vskip2pt
{\footnotesize Before the details, here is the whole paper in eleven short steps. The question it answers is deceptively simple: \emph{why did the model choose this word and not another?}}
\vskip4pt
{\footnotesize
\begin{columns}[T]
\begin{column}{0.465\textwidth}
\begin{enumerate}\setlength{\itemsep}{2pt}
  \item Classic interpretability methods work well for \textbf{classification} (\emph{cat} vs \emph{dog}), but not for language models, which choose among thousands of possible words.
  \item A single word can be chosen for many reasons \textbf{at once}: meaning, tense, number, syntax, style.
  \item Asking plainly ``\emph{why this word?}'' tends to produce \textbf{confusing} explanations.
  \item The authors pose a different question: ``\emph{why this word \textbf{rather than} that one?}''.
  \item They call this a \textbf{contrastive explanation}.
  \item Instead of explaining everything, it explains \textbf{only the difference} between two candidates.
\end{enumerate}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.465\textwidth}
\begin{enumerate}\setlength{\itemsep}{2pt}\setcounter{enumi}{6}
  \item Example: explain why the model wrote \emph{themselves} and not \emph{herself}.
  \item The method identifies \textbf{which parts of the context} favour one option over the other.
  \item This surfaces the semantic and grammatical factors in a form that is far \textbf{clearer to humans}.
  \item The resulting explanations are \textbf{more compact and more interpretable} than traditional saliency.
  \item The central idea: \textbf{to understand a decision is to understand the boundary that separates one alternative from another} --- not to explain the whole space of possibilities.
\end{enumerate}
\end{column}
\end{columns}}
```

# Paper 2 in plain words: the contrastive idea

```{=latex}
\vskip2pt
{\footnotesize \textbf{The problem.} A language model picks the next word among $\sim 50{,}000$ options. Asking ``\emph{why this word?}'' gives a vague answer --- it just points at the word right before, and blurs grammar, number and meaning together. We cannot see what really drove the choice.}
\vskip4pt
\begin{block}{The fix: ask a contrastive question}
Instead of ``\emph{why did it say $X$?}'', ask ``\emph{why $X$ \textbf{rather than} $Y$?}'' --- comparing against a rival turns a vague question into a precise one.
\end{block}
\begin{block}{Worked example}
Input: ``Many teenagers were helping \rule{1cm}{0.4pt}''. The model says \textbf{themselves} (not \emph{herself}).\\[2pt]
\textbf{Why ``themselves'' rather than ``herself''?} $\to$ because of the word \textbf{``teenagers''} --- it is \emph{plural}, so it requires \emph{themselves}. The plural subject is what tipped the decision.
\end{block}
\begin{center}\textbf{Change the rival, change the question:} the rival ($Y$) aims the explanation at one specific decision.\end{center}
```

# Under the hood: logits and the softmax {.shrink}

```{=latex}
\vskip2pt
{\footnotesize Before the subtraction itself, three pieces of machinery must be made explicit. The first is how a language model turns its internal computation into a single chosen word.}
\vskip4pt
\begin{center}
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig10.pdf}
\end{center}
\vskip2pt
\begin{block}{Logits and the softmax, defined}
A language model holds a fixed \textbf{vocabulary} (about $50{,}000$ word-pieces for GPT-2). At each step it assigns \emph{every} word in that vocabulary a raw, unnormalised score, called a \textbf{logit}; these scores may be positive or negative and do not sum to one. The \textbf{softmax} function then maps the full set of logits to a \textbf{probability distribution} --- values between $0$ and $1$ that sum to $1$. The word with the largest logit receives the largest probability and is selected as the output.
\end{block}
```

# Under the hood: the model in three zones

```{=latex}
\vskip2pt
{\footnotesize The second piece is a map of the model in three zones. It makes precise \emph{where} the method acts and, just as importantly, where it does not.}
\vskip1pt
\begin{center}
\scalebox{0.78}{%
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig11.pdf}}
\end{center}
\vskip1pt
\begin{block}{Three terms, defined}
\textbf{Embedding} --- the numeric vector into which each input word is converted before the network reads it. \quad \textbf{Logit} --- the raw output score of a candidate word, before the softmax. \quad \textbf{Hook} --- an instrumentation point that reads an internal value (or its gradient) without altering the computation. The method is \textbf{post-hoc}: the weights of Zone 2 stay frozen.
\end{block}
```

# Under the hood: the gradient as a sensitivity {.shrink}

```{=latex}
\vskip2pt
{\footnotesize The third piece is how a word's influence is actually measured. It is read from a \textbf{gradient} of the contrastive score with respect to the input embedding.}
\vskip2pt
\begin{center}
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig12.pdf}
\end{center}
\vskip1pt
\begin{block}{A rate, not a difference}
The gradient is a \textbf{rate}: the change in the contrastive score \emph{per unit} change in the input embedding --- a slope, not the change itself. The two gradient methods use this slope (one cheap backward pass). The \textbf{erasure} method instead measures a \textbf{finite difference}: it deletes the word, re-runs the model, and reads how much the score actually moved. \textbf{Gradients approximate the perturbation; erasure performs it.}
\end{block}
```

# The flagship example: "barking", not "crying" {.shrink}

```{=latex}
\vskip2pt
{\footnotesize The paper's opening example (Table~1). The model reads ``\emph{Can you stop the dog from \rule{0.7cm}{0.4pt}}'' and predicts \textbf{barking}. Each row colours the input: \textcolor{red!70!black}{red} raises the probability of the target, \textcolor{blue!70!black}{blue} lowers it, white means little influence.}
\vskip3pt
\begin{center}
\scalebox{0.92}{%
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig13.pdf}}
\end{center}
\vskip2pt
\begin{block}{Reading the three rows}
\textbf{(1) Plain saliency is uninformative}: almost all the weight lands on the preceding token ``from''. \quad \textbf{(2) Against ``crying''}, the word \textbf{``dog''} lights up --- dogs bark, they do not cry. \quad \textbf{(3) Against ``walking''}, the word \textbf{``stop''} lights up --- one stops an \emph{unwanted} behaviour. \textbf{Change the foil, change the evidence.} (In the paper's other example, ``Spanish'' \emph{vs} ``Portuguese'' makes the word ``Spain'' salient.)
\end{block}
```

# The three methods, in plain words {.shrink}

```{=latex}
\vskip2pt
{\footnotesize All three answer the \emph{same} question --- \textbf{how much does this word push towards the target rather than the foil?} --- and rank the words by that push. They differ only in the \textbf{instrument} used to read it.}
\vskip1pt
{\tiny \textbf{Formally (reference):} contrastive slope $g^{*}=\nabla_{x_i}\big(q(y_t|x)-q(y_f|x)\big)$; \; $S^{*}_{GN}=\lVert g^{*}\rVert_{L1}$, \; $S^{*}_{GI}=g^{*}\cdot x_i$, \; erasure deletes $x_i$ and re-measures.}
\vskip3pt
\begin{block}{The three instruments}
\textbf{1 -- Gradient Norm} (``does it matter?''): the \textbf{size} of the slope, no direction. Cheap; needs the model's internals.\\[2pt]
\textbf{2 -- Gradient $\times$ Input} (``towards which option?''): the same slope, now \textbf{signed} ($+$ target, $-$ foil). Cheap, and more informative than the norm.\\[2pt]
\textbf{3 -- Input Erasure} (``delete it and see''): no slope --- \textbf{remove the word, re-run, measure the real change}. Direct and needs no internals, but \textbf{expensive} (one run per word).
\end{block}
\begin{block}{In one line}
\textbf{Gradient $\times$ Input} is the practical winner (cheap, signed, best or near-best everywhere); \textbf{Erasure} is the most faithful but costly; the \textbf{Norm} is the simplest but says the least. \quad {\scriptsize(Gradients \emph{approximate} the push; erasure \emph{performs} it.)}
\end{block}
```

# Grading an explanation objectively: BLiMP {.shrink}

```{=latex}
\vskip2pt
{\footnotesize \textbf{BLiMP} (Warstadt et al., 2020) provides \emph{minimal pairs}: two sentences differing in one spot, one grammatical and one not. The token that enforces the rule is the \textbf{known evidence}, fixed in advance.}
\vskip2pt
\begin{center}
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig14.pdf}
\end{center}
\vskip1pt
\begin{columns}[T]
\begin{column}{0.465\textwidth}
\begin{block}{The setup}
\textbf{5 phenomena / 12 paradigms} (anaphor, argument structure, determiner--noun, NPI, subject--verb). Models: \textbf{GPT-2} (1.5B), \textbf{GPT-Neo} (2.7B). The test: does the \textbf{top-ranked token} land on the known evidence?
\end{block}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.465\textwidth}
\begin{block}{The alignment metrics ($\mathcal{E}$: evidence, $\mathcal{S}$: saliency)}
\textbf{Dot product} $\mathcal{S}\!\cdot\!\mathcal{E}$ (\textbf{higher} better); \textbf{Probes needed} --- rank of the first evidence token (\textbf{lower} better); \textbf{MRR} --- mean of $1/\text{rank}$ (\textbf{higher} better).
\end{block}
\end{column}
\end{columns}
\vskip1pt
\begin{block}{Why this matters: from ``plausible'' to ``gradeable''}
BLiMP turns interpretability into a \textbf{gradeable exam}. Without it, the most one can say is \emph{``my explanation looks reasonable''}; with it we can ask \emph{``did the method land on exactly the word grammar says should matter?''} That shift --- \textbf{plausible} $\to$ \textbf{checkable} --- is what lets Yin \& Neubig \textbf{prove} contrastive explanations beat traditional saliency.
\end{block}
```

# RQ1 --- contrastive explanations align better {.shrink}

```{=latex}
\vskip2pt
{\footnotesize \textbf{RQ1: do contrastive explanations find the linguistically appropriate evidence?} Mean alignment with the known BLiMP evidence (here MRR, GPT-2; \emph{higher is better}). Contrastive variants $S^{*}$ in \textcolor[HTML]{2E7D32}{green}.}
\vskip2pt
\begin{center}
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig15.pdf}
\end{center}
\vskip2pt
\begin{columns}[T]
\begin{column}{0.484\textwidth}
\begin{block}{What the bars say}
For both GPT-2 and GPT-Neo, and across all three metrics, the \textbf{contrastive} variant beats its non-contrastive twin; the non-contrastive ones do not even always beat the random baseline.
\end{block}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.446\textwidth}
\begin{block}{The distance effect}
The \textbf{further} the evidence token sits from the prediction, the \textbf{larger} the contrastive advantage --- contrastive explanations capture \textbf{longer-range} decisions best.
\end{block}
\end{column}
\end{columns}
```

# RQ2 --- contrastive explanations help humans {.shrink}

```{=latex}
\vskip2pt
{\footnotesize \textbf{RQ2: do the explanations help a person predict the model?} (\textbf{simulatability} --- shown the input + the explanation, can a person guess the model's next word?). \textbf{10 ML graduate students}, \textbf{4{,}000} judgments; the model is right exactly 50\% of the time, so guessing the grammatical option does not help. \textbf{Higher is always better.}}
\vskip2pt
\begin{columns}[T]
\begin{column}{0.419\textwidth}
\centering
\footnotesize
\begin{tabular}{@{}l cccc@{}}
\hline
Method & Acc. & Corr. & Inc. & Use.\\
\hline
None & 61.38 & 74.50 & 48.25 & --\\
$S_{GI}$ & 64.00 & 78.25 & 49.75 & 62.12\\
$S^{*}_{GI}$ & \textbf{65.62} & \textbf{79.00} & 52.25 & \textbf{63.88}\\
$S_{E}$ & 63.12 & 79.00 & 47.25 & 46.50\\
$S^{*}_{E}$ & \textbf{64.62} & 77.00 & 52.25 & \textbf{64.88}\\
\hline
\end{tabular}
\vskip4pt
{\scriptsize\raggedright
\textbf{Rows.} \textbf{None} $=$ no explanation shown; \; \textbf{$S$} $=$ non-contrastive, \textbf{$S^{*}$} $=$ contrastive (the $*$); \; \textbf{$GI$} $=$ gradient$\times$input, \textbf{$E$} $=$ erasure.\\[3pt]
\textbf{Columns.} \textbf{Acc.} $=$ \% of times the user guessed the model's word; \; \textbf{Corr.}/\textbf{Inc.} $=$ the same, on cases where the model itself was right / wrong; \; \textbf{Use.} $=$ \% of explanations users called helpful.\par}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.512\textwidth}
{\footnotesize \textbf{Conclusions, read straight from the numbers:}}
\begin{enumerate}\footnotesize\setlength\itemsep{0pt}
  \item \textbf{Any explanation helps.} With none, users hit $61.38$; every method climbs to $\geq 63$ $\Rightarrow$ explanations make the model more predictable.
  \item \textbf{Contrastive beats plain.} grad$\times$input $64.00\!\to\!\textbf{65.62}$; erasure $63.12\!\to\!\textbf{64.62}$. The starred ($S^{*}$) version wins in \emph{both} families.
  \item \textbf{It helps most where intuition fails.} On \emph{Incorrect} cases (model $\neq$ grammatical guess): $48.25$ (None) $\to\!\textbf{52.25}$ (both $S^{*}$) --- the largest jump.
  \item \textbf{Users feel it too.} ``Useful'' rises with contrast: grad$\times$input $62.12\!\to\!\textbf{63.88}$; erasure leaps $46.50\!\to\!\textbf{64.88}$.
  \item \textbf{Best overall: $S^{*}_{GI}$ ($65.62$)} --- cheap and most accurate; $S^{*}_{E}$ is close and rated the most useful.
\end{enumerate}
{\footnotesize $\Rightarrow$ \textbf{contrastive explanations give a more faithful, human-usable picture of the model.}}
\end{column}
\end{columns}
```

# RQ3 --- clustering decisions by their cause {.shrink}

```{=latex}
\vskip2pt
{\footnotesize \textbf{RQ3: do different decisions need different evidence, and does that evidence reveal coherent linguistic concepts?} Represent each \textbf{foil} by the saliency vector it produces against a target, then cluster those vectors.}
\vskip3pt
\begin{center}
\includegraphics[width=\linewidth,height=0.46\textheight,keepaspectratio]{imgs/fig16.pdf}
\end{center}
\vskip2pt
\begin{columns}[T]
\begin{column}{0.465\textwidth}
\begin{block}{Clusters recovered without supervision}
A \textbf{male} pronoun target $\to$ a cluster of \textbf{female} pronouns; an \textbf{animate} noun $\to$ a cluster of \textbf{inanimate} nouns; a \textbf{singular} noun $\to$ a cluster of \textbf{plural} foils. These differ from word-embedding neighbours --- they reflect \textbf{what the model uses to decide}.
\end{block}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.465\textwidth}
\begin{block}{Which evidence for which decision}
\textbf{Gender} (pronouns, determiners): gendered proper nouns (``Veronica'', ``he''). \quad \textbf{Number/verbs}: other verb forms. \quad \textbf{Adjectives}: semantically related words (to tell ``black'' from colours, ``relativity'' matters). \quad \textbf{Numbers}: enumeration words (``age'', ``least'').
\end{block}
\end{column}
\end{columns}
```

# Limitations, and what to take away {.shrink}

```{=latex}
\vskip2pt
{\footnotesize Where the method is fragile, and what it contributes.}
\vskip3pt
\begin{columns}[T]
\begin{column}{0.465\textwidth}
\begin{block}{Limitations}
\begin{itemize}\setlength{\itemsep}{2pt}
  \item \textbf{Foil selection} is consequential: open-ended generation has no rule for which foil to use, and changing it changes the result.
  \item \textbf{Plausibility $\neq$ faithfulness}: matching human intuition does not prove the saliency reflects the true computation.
  \item \textbf{Cost}: erasure is the most direct but scales poorly with length and foil-space size.
\end{itemize}
\end{block}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.465\textwidth}
\begin{block}{Contributions}
\begin{itemize}\setlength{\itemsep}{2pt}
  \item Extended \textbf{three} saliency methods to the contrastive setting (norm, grad$\times$input, erasure).
  \item Proposed \textbf{three} evaluations: alignment (BLiMP), human simulatability, and clustering-based analysis.
  \item Contrastive explanations give a \textbf{more intuitive, fine-grained} interpretation of LMs.
\end{itemize}
\end{block}
\end{column}
\end{columns}
\vskip2pt
\begin{block}{The thread back to Paper 1}
Rajani \textbf{verbalises} reasoning (optimised for \textbf{plausibility}); Yin \& Neubig \textbf{localise} evidence (aimed at \textbf{faithfulness}). Two ends of the same question: \emph{how do language models reason?}
\end{block}
```

# Recap - the two papers at a glance {.shrink}

```{=latex}
{\footnotesize \textbf{Recap.} Both methods explain a trained \textbf{language model (LM)} \emph{from the outside} (inputs $\to$ outputs) --- but the LM itself differs per paper.}
\begin{columns}[T]
\begin{column}{0.465\textwidth}
\begin{block}{Paper 1 $\cdot$ Rajani et al. (2019) --- CAGE}
\footnotesize \textbf{Verbalise the reasoning}
\begin{itemize}\footnotesize
  \item \textbf{Explains:} a BERT classifier (few answer options).
  \item \textbf{What:} a natural-language narrative.
  \item \textbf{Goal:} plausibility + accuracy.
  \item \textbf{Family:} generated explanation.
  \item \textbf{Nature:} black-box (internals not inspected).
\end{itemize}
\end{block}
\end{column}\hspace{0.045\textwidth}
\begin{column}{0.465\textwidth}
\begin{block}{Paper 2 $\cdot$ Yin \& Neubig (2022)}
\footnotesize \textbf{Localise the evidence}
\begin{itemize}\footnotesize
  \item \textbf{Explains:} a generative LM --- GPT-2 / GPT-Neo ($\sim 50{,}000$ tokens).
  \item \textbf{What:} attributes the output to input tokens.
  \item \textbf{Goal:} faithfulness, fine-grained evidence.
  \item \textbf{Family:} contrastive attribution (gradient/erasure).
  \item \textbf{Nature:} gradients (white-box) or perturbation.
\end{itemize}
\end{block}
\end{column}
\end{columns}
\begin{center}\includegraphics[width=0.6\linewidth,height=0.16\textheight,keepaspectratio]{imgs/fig17.pdf}\end{center}
```

# Open questions

```{=latex}
\vskip1em
\begin{itemize}
  \item Can generated explanations (Paper 1) be \textbf{constrained to be faithful} via contrastive saliency (Paper 2)?
  \item What is the right way to \textbf{choose foils} for open-ended generation?
  \item For regulated settings (EU AI Act --- the EU's AI regulation, Art. 13), is a \textbf{plausible} explanation enough, or is \textbf{faithfulness} legally required?
\end{itemize}
\vskip1em
\begin{center}{\Large\textbf{Discussion?}}\end{center}
```

# Thank you

```{=latex}
\vfill
\begin{center}{\Huge\color{green!35!black} Thank You!}\end{center}
\vfill
```

# References {.allowframebreaks}

```{=latex}
\footnotesize
\begin{itemize}\itemsep2pt
\item Black, S., Gao, L., Wang, P., Leahy, C., Biderman, S. (2021). \emph{GPT-Neo: Large Scale Autoregressive Language Modeling with Mesh-TensorFlow}. EleutherAI.
\item Devlin, J., Chang, M.-W., Lee, K., Toutanova, K. (2019). \emph{BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding}. NAACL-HLT.
\item Lipton, P. (1990). \emph{Contrastive Explanation}. Royal Institute of Philosophy Supplement 27: 247--266.
\item Mostafazadeh, N., Chambers, N., He, X., et al. (2016). \emph{A Corpus and Cloze Evaluation for Deeper Understanding of Commonsense Stories}. NAACL-HLT.
\item Papineni, K., Roukos, S., Ward, T., Zhu, W.-J. (2002). \emph{BLEU: a Method for Automatic Evaluation of Machine Translation}. ACL.
\item Radford, A., Narasimhan, K., Salimans, T., Sutskever, I. (2018). \emph{Improving Language Understanding by Generative Pre-Training}. OpenAI tech report.
\item Radford, A., Wu, J., Child, R., Luan, D., Amodei, D., Sutskever, I. (2019). \emph{Language Models are Unsupervised Multitask Learners}. OpenAI tech report.
\item Rajani, N. F., McCann, B., Xiong, C., Socher, R. (2019). \emph{Explain Yourself! Leveraging Language Models for Commonsense Reasoning}. ACL, 4932--4942.
\item Rajpurkar, P., Zhang, J., Lopyrev, K., Liang, P. (2016). \emph{SQuAD: 100,000+ Questions for Machine Comprehension of Text}. EMNLP.
\end{itemize}
```

```{=latex}
\footnotesize
\begin{itemize}\itemsep2pt
\item Speer, R., Chin, J., Havasi, C. (2017). \emph{ConceptNet 5.5: An Open Multilingual Graph of General Knowledge}. AAAI.
\item Talmor, A., Herzig, J., Lourie, N., Berant, J. (2019). \emph{CommonsenseQA: A Question Answering Challenge Targeting Commonsense Knowledge}. NAACL-HLT, 4149--4158.
\item Vaswani, A., Shazeer, N., Parmar, N., et al. (2017). \emph{Attention Is All You Need}. NeurIPS.
\item Warstadt, A., Parrish, A., Liu, H., et al. (2020). \emph{BLiMP: The Benchmark of Linguistic Minimal Pairs for English}. TACL 8: 377--392.
\item Yin, K., Neubig, G. (2022). \emph{Interpreting Language Models with Contrastive Explanations}. EMNLP, 184--198.
\item Zellers, R., Bisk, Y., Schwartz, R., Choi, Y. (2018). \emph{SWAG: A Large-Scale Adversarial Dataset for Grounded Commonsense Inference}. EMNLP.
\end{itemize}
```
