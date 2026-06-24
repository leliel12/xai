---
title: "\\emoji{wtf} XAI: Chess Knowledge in AlphaZero \\& Interpreting Stable Diffusion"
bibliography: references.bib

---

# Disclaimer

\input{../disclaimer.tex}

---

# Paper 1

\begin{center}
\includegraphics[width=0.85\columnwidth]{imgs/paper1.png}
\end{center}

[@mcgrath2022acquisition]

---

# Learning from machines (AlphaZero)

:::: columns
::: column

- Most methods we have looked so far try to interpret algorithms trained on human-generated data and labels
    - These interpretations may resemble human-understandable representations only because they learned from such data
- Can we interpret what the algorithms has been learning through its self-play training process?

:::
::: column

![](imgs/manochess.png)

:::
::::

---

# Learning from Machines: Three-Pronged Acquisition

- **Probe for concepts**
  - How closely is AlphaZero's internal representation **related to** chess concepts humans have already created?
  - Detection of human concepts from network activations
- **Study behavioral changes**
  - How do changing representations give rise to changing behaviors?
  - Evolution of AlphaZero vs. human's strategy in openings
- **Investigate activations directly**
  - Unsupervised methods using non-negative matrix factorization (NMF) and direct measure of covariance to discover concepts not represented by humans in the past

---

# Interpretability

**Concept-based (post-hoc) interpretability**

- **Network probing:** detect human concepts from internal activations
  using linear classifiers (*concept activation vectors*)
- **Important features:** which concepts matter most for model predictions?
- **Mechanistic understanding:** what algorithms do individual layers implement?
- **Key challenge:** probing measures *correlation*, not *causation* —
  a concept linearly decodable from activations may not causally drive behavior

**Explainability in reinforcement learning**

- **Structural causal models:** simulate interventions on actions
  to answer counterfactual questions (*what if a different move was played?*)
- **Reward difference explanations:** why was action $a$ chosen over $a'$?
  Quantify the expected reward gap
- **Behavioral trajectory analysis:** identify critical decision points
  where the agent's policy changes most dramatically

---

# Chess as Testing Ground for AI Interpretability

Can humans learn the machine's strategy?

- Does not rely on human-labeled data
- Tree-based organization / saliency maps
- Natural language processing to generate move-by-move commentary
- **This paper:** captures "intuitive" aspect of chess play by understanding networks that produce value assessment (**v**) and candidate move (**p**)

$$\mathbf{p},\ v = f_\theta(\mathbf{z}^0)$$

## In plain English

$\mathbf{z}^0$ is the board position fed to the network; $\mathbf{p}$ is
a probability distribution over legal moves (what AlphaZero *wants* to play),
and $v \in [-1, 1]$ is its estimate of who is winning.
The question of the paper is: *what does $f_\theta$ know about chess
to produce these outputs?*

---

# AlphaZero: Network Structure and Training {.fragile}

:::: columns
::: column

\includegraphics[width=.9\columnwidth]{imgs/alphazero_network.png}

:::
::: column

$$\mathbf{p},\ v = f_\theta(\mathbf{z}^0)$$

\vspace{0.4cm}

$$\mathbf{z}^l = f^l(\mathbf{z}^{l-1}) = \text{ReLU}\!\left(\mathbf{z}^{l-1} + g^l(\mathbf{z}^{l-1})\right)$$

\vspace{0.4cm}

$$\mathbf{z}^l = f^{1:l}(\mathbf{z}^0) = f^l \circ \cdots \circ f^2 \circ f^1(\mathbf{z}^0)$$

:::
::::


---

# AlphaZero: Network Structure and Training {.fragile}

:::: columns
::: column

\includegraphics[width=.9\columnwidth]{imgs/alphazero_network.png}

:::
::: column

## In plain English
In the diagram, each box in the red rectangle is one residual block.
The **"add; ReLU"** arrow inside each box is where the network adds
the unmodified input to the learned transformation — instead of
replacing it. This repeats 20 times, building progressively more
abstract representations of the board.

:::
::::

---

# Probing for Concepts - Encoding of Human Conceptual Knowledge {.fragile}

\fontsize{11pt}{10pt}
\textbf{Question:} Can human concepts be easily predicted from the network's internal representation?

\textbf{Concept:} User-defined function mapping network input to real line:

$$c(\mathbf{z}^0) = \begin{cases} 1 & \text{if } \mathbf{z}^0 \text{ contains a bishop-pair U+2657 for the playing side} \\ 0 & \text{otherwise} \end{cases}$$

\textbf{Approach:} Train a sparse linear regression model from activations $\mathbf{z}^l$ at layer $l$ and training step $t$ to human concept $j$

\fontsize{9pt}{8pt}
:::  columns
:::: column
## In plain English
A *concept* is any chess idea a human can define on a board position —
from simple material facts (does the player have the bishop pair?)
to complex positional ideas (is the king safe?).
If a simple linear model can predict $c_j$ from $\mathbf{z}^l$,
the concept is **linearly encoded** in that layer.
::::

:::: column
## Key Insight
The network was never explicitly trained to represent chess concepts —
it only learned to predict moves and outcomes via self-play.
Finding linearly decodable concepts means AlphaZero **spontaneously
developed human-like internal representations**.
::::
:::

---

# Probing Concept Learning: Details {.fragile}

- **Data:** Randomly sample training, validation, test data from ChessBase archive, compute concept values and AlphaZero activations
- **Procedure:** For concept $i$, layer $l$, training step $t$, solve:

$$\mathbf{w}_{jlt},\ b_{jlt} = \min_{\mathbf{w},b} \frac{1}{N}\left\|\mathbf{w}^T \mathbf{Z}^l_t + b\mathbf{1} - \mathbf{c}_j\right\|^2_2 + \lambda\|\mathbf{w}\|_1 + \lambda|b|$$

- **Controls:** Regression from $\mathbf{z}^0$ and random concept regression
- **Evaluation:** $R^2$ value, fraction of variance in concept explained by network activation

:::  columns
:::: column
## In plain English
Fit a Lasso regression: predict concept $c_j$ from activations $\mathbf{z}^l$.
The $\ell_1$ penalty keeps only the **few neurons** that actually encode the concept.
::::

:::: column
## Key Insight
High $R^2$ = the concept is **linearly readable** from that layer.
Repeat for every layer $l$ and checkpoint $t$ $\rightarrow$ a map of *what, when, and where*
each concept is learned.
::::
:::

---

# Evolution of Human Concepts in AlphaZero 1/2

:::: columns
::: {.column width="27%"}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/human_concepts_evolution.png}
\end{center}

:::
::: {.column width="60%"}


\vspace{1em}
Each surface is a *what–when–where* plot: probe $R^2$ as a function of
**network depth** (Block) and **training time** (Training steps).

- **x-axis:** training steps ($10^0$ → $10^6$) — *when* is it learned?
- **y-axis:** block index (1–20) — *where* in the network?
- **z-axis:** test accuracy — *how well* is the concept encoded?

:::
::::

---

# Evolution of Human Concepts in AlphaZero 2/2

:::: columns
::: {.column width="27%"}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/human_concepts_evolution.png}
\end{center}

:::
::: {.column width="60%"}

\vspace{1em}
- A surface that rises early and stays high means AlphaZero learned
that concept **fast and robustly** (e.g. `in_check`).
- A surface that stays flat means the concept is **never linearly encoded** (e.g. `threats_t_ph` — still dark/purple throughout).

- Simple tactical concepts (`in_check`, `can_capture_queen`) emerge
**early and in middle layers**. Complex strategic concepts
(`total_t_ph`, `has_mate_threat`) require **more training steps**
and tend to concentrate in **deeper layers** — mirroring how
humans learn chess: tactics before strategy.

:::
::::

---

# Key Findings ---

1. **Grokking:** Most concepts emerge abruptly around **32,000 training steps** accuracy is near zero before, then jumps and stabilizes. AlphaZero does not learn gradually; it *clicks*.

2. **Information drop in deep layers:** for some concepts, probe accuracy
   *decreases* in the last blocks — the network re-encodes information
   in a less linearly-separable form as it approaches the output heads.

3. **Highly-distributed representations:** some concepts **cannot** be probed —
   sparsity forces the probe to use few neurons, but if the concept is
   spread across many neurons simultaneously, a sparse linear probe cannot
   recover it.

4. **Learning from errors:** positions where the probe fails systematically
   may reflect a genuine **"difference of opinion"** between AlphaZero
   and Stockfish — not a probe failure, but AlphaZero evaluating the
   position by a different (possibly superior) criterion.

## Key Insight
Findings 3 and 4 are honest about the method's limits:
absence of a detectable concept $\neq$ absence of the concept —
it may simply be encoded in a way a **linear probe cannot see**.

---

# Challenges for Concept Probing

1. What's the right **probing architecture**?
2. How should we interpret **complex or subjective concepts**?
3. When can we definitively say a **concept is represented**?
4. When we train a probe, we cannot tell if we are getting a **confounder or the concept** itself

---

\begin{center}
\vfill
\Large Progression through AlphaZero and Human History
\vfill
\end{center}

---

# Progression of Knowledge

- Recall: $\mathbf{p},\ v = f_\theta(\mathbf{z}^0)$

- **Question:** How does the progression of AlphaZero's knowledge compare to that of humans?
- **Key Findings:**
  - AlphaZero: Start from a uniform prior, then narrow down.
  - Humans: Start from a concentrated prior, then expand.

---

# Progression through Human History

\begin{center}
\includegraphics[width=0.88\columnwidth]{imgs/progression_human_history.png}

\vfill

Each color represents a first move (e.g. e4, d4, Nf3), with its area showing how often humans played it across history — serving as a reference to compare whether AlphaZero's training recapitulates or diverges from human chess evolution.

\end{center}


---

# Progression through AlphaZero History

\begin{center}
\includegraphics[width=0.88\columnwidth]{imgs/progression_alphazero_history.png}

\vfill

The three panels show \textbf{three independent AlphaZero training runs}: 
the distribution is remarkably consistent across runs --- AlphaZero converges 
to similar move preferences regardless of random initialization. Before 64,000 
steps, chaotic exploration dominates; after that, the distribution stabilizes 
abruptly (\textbf{grokking}). Compared to human history, AlphaZero settles on 
a mix of \texttt{d4} and \texttt{e4} but with a more diverse and volatile 
distribution --- \textbf{it does not recapitulate human chess history}, but 
finds its own path.

\end{center}


---

# Progression of AlphaZero's Chess Knowledge

**Methodology:** At different training checkpoints (up to 128k steps),
examine AlphaZero's move tendencies and concept probe accuracy.

> **Primary Takeaways**
>
> 1. AlphaZero learns standard opening theory **early** —
>    move preferences stabilize and match known theory within the first 64k steps
> 2. AlphaZero learns **material values before positional concepts** —
>    piece counts are encoded first; king safety and mobility come later
>
> **Both findings reinforce that AlphaZero learns basic human chess concepts first**

## Key Insight
This mirrors the curriculum of a human chess student:
openings and material counting are taught before strategic positional play.
AlphaZero rediscovers this ordering **from scratch**, via self-play alone —
with no exposure to human games or pedagogy.

---

# Opening Theory Knowledge

\begin{enumerate}
\item $\sim$30--60k: AlphaZero plays \textit{1. e4/d4} the majority of the time
\item $\sim$45k: AlphaZero considers \textit{2. d4} before opting for \textit{2. Nf3}
\item $\sim$45k: AlphaZero plays standard responses to \textit{1. e4}
\end{enumerate}

\begin{center}
\includegraphics[width=0.85\columnwidth]{imgs/opening_theory_plots.png}
\end{center}

- **Graph 1:** What move should White play to open the game?
- **Graph 2:** What should White play on move 2, after Black responds with `e5`?
- **Graph 3:** How should Black respond to White's opening move `e4`?


---

# Material vs. Positional Knowledge

::::: columns

::: {.column width="27%"}
\includegraphics[width=\columnwidth]{imgs/material_positional_plots.png}
::::

::: {.column width="60%"}

**Top — Material:**

The *relative value of pieces*, measured in pawns. Standard human values are: Queen=9, Rook=5, Bishop=3, Knight=3. AlphaZero independently rediscovers approximately these values by ~100k steps.

**Bottom — Positional concepts:**

- **Material:** piece count advantage
- **King Safety:** exposure to attacks
- **Mobility:** number of legal moves available
- **Space:** board control
- **Threats:** can capture next move
- **Imbalance:** asymmetric piece differences (e.g. bishop vs. knight)

::::

:::::

**The story they tell together:** AlphaZero first learns to count pieces (material, ~30k steps), and only later develops subtler positional concepts like king safety and mobility — exactly the order in which a human learns chess.


---

# Training Progression Assessment: GM Vladimir Kramnik

:::: columns

::: {.column width="80%"}

Qualitative evaluation by World Chess Champion Vladimir Kramnik,
who analyzed AlphaZero's play at three training checkpoints:

1. **16k to 32k steps:** understands material value in complex positions —
   knows which pieces are worth sacrificing and which are not

2. **32k to 64k steps:** develops king safety awareness in imbalanced positions —
   starts recognizing when the king is genuinely at risk

3. **64k to 128k steps:** combines king safety with material sacrifices
   in complex positions — a hallmark of grandmaster-level strategic play

:::

::: {.column width="18%"}

\includegraphics[width=\columnwidth]{imgs/kramnik_photo.png}

:::

::::

## Key Insight:

- Tactical skills appear to precede positional skills as AlphaZero learns
- This is a **human expert's qualitative confirmation** of what the concept
probes showed quantitatively: AlphaZero's learning curriculum —
material first, positional concepts later — mirrors how chess mastery
develops in humans.

---

\begin{center}
\vfill
\Large Exploring Additional Feature Detectors\\in AlphaZero Network
\vfill
\end{center}

---

# Exploring Activations with Unsupervised Methods

**Goal:** Find feature detectors embedded within the network —
*without* using predefined human concepts as supervision.

## In plain English
Unlike probing (which asks *"is concept X encoded here?"*),
these methods ask *"what patterns exist here?"* with no
human concept assumed in advance.

## Key Insight
If unsupervised methods independently recover human-like concepts,
it strengthens the case that AlphaZero's representations are genuinely
structured around chess knowledge — not an artifact of the linear probe design.

---

# Unsupervised Methods: Details

a) **Non-Negative Matrix Factorization (NMF):** decompose each layer's
   activations into a small set of additive factors — each factor
   represents a pattern of co-activating channels across board positions

b) **Input-activation correlation:** measure the covariance between
   each channel's activation and the raw input board —
   reveals which input features drive individual neurons

## Primary Takeaway

Individual layers and channels encode *feature detectors*
related to human-recognizable chess concepts —
discovered **without any concept labels**.

---

# Approach #1: Non-Negative Matrix Factorization

**Idea:** compress each layer's activations into $K < C$ interpretable factors,
then visualize each factor on the board to find *feature detectors*.

**Step 1:** Stack all activations for layer $l$ with $C$ channels:
$\hat{\mathbf{Z}}^l \in \mathbb{R}^{NHW \times C}$

**Step 2:** Factorize $\hat{\mathbf{Z}}^l \approx \mathbf{\Omega}_\text{all}\mathbf{F}$:

$$\mathbf{F}^*,\, \mathbf{\Omega}^*_\text{all} = \min_{\mathbf{F},\,\mathbf{\Omega}_\text{all}} \left\|\hat{\mathbf{Z}}^l - \mathbf{\Omega}_\text{all}\mathbf{F}\right\|^2_2 \qquad \mathbf{F},\ \mathbf{\Omega}_\text{all} \geq 0$$

- $\mathbf{\Omega}_\text{all} \in \mathbb{R}^{NHW \times K}$ — factor scores per board square
- $\mathbf{F} \in \mathbb{R}^{K \times C}$ — which channels compose each factor

**Step 3:** For each factor $k$ and input $n$, visualize $\mathbf{\Omega}_k$ on the board.

---

# Approach #1: Results

## In plain English
Each factor $k$ groups channels that fire together.
Plotting its scores on the board reveals *where* that pattern activates —
if it traces diagonals or controlled squares, the network learned that concept
without being told to.

## Key Insight
These patterns emerge with **no concept labels** —
NMF discovers structure purely from activation co-occurrence.
If the recovered factors resemble human chess concepts,
it independently confirms the probing results.

---

# Results: NN Matrix Factorization Analysis

\begin{center}
\includegraphics[width=0.70\columnwidth]{imgs/nmf_results.png}
\end{center}

---

# Approach #2: Input-Activation Covariance Analysis

**Idea:** for each channel $i$ in layer $l$, measure how strongly its
activation correlates with each input feature — then visualize on the board.

**Step 1:** Compute covariance between channel $i$'s activation $z^l_i$
and the raw input board $\mathbf{z}^0$:

$$\text{cov}(z^l_i, \mathbf{z}^0) = \mathbb{E}\left[z^l_i \mathbf{z}^0\right] - \mathbb{E}\left[z^l_i\right]\mathbb{E}\left[\mathbf{z}^0\right]$$

**Step 2:** Visualize $\text{cov}(z^l_i, \mathbf{z}^0)$ on the board to find *feature detectors*.

## In plain English
If a neuron fires whenever there is a bishop on a particular diagonal,
its covariance with those input squares will be high —
the map reveals *what the neuron is looking at* in the input.

## Key Insight
Unlike NMF (which finds group patterns across channels),
this method zooms into **individual neurons** —
asking *what specific input configuration drives this unit?*

---

# Results: Input-Activation Covariance Analysis

Detecting Move-Types from a Square:

\begin{itemize}
\item 1--2) Diagonal-Attacking Pieces (Queen, Bishop)
\item 3--4) Horizontally-Attacking Pieces (Queen, Rook)
\item 5) Both
\end{itemize}

\begin{center}
\includegraphics[width=0.82\columnwidth]{imgs/covariance_results.png}

\vfill

5 covariances with different channels for the square (5, 4) E4

\end{center}

---

# Conclusion: Key Findings (Paper 1)

1. Human-Defined Concepts can be regressed from the AlphaZero network
   - Despite never being trained on a human game of chess
2. As training progresses, AlphaZero understands basic concepts (openings, material) before more complex ones (king safety, mobility)
3. Feature Detectors of human-recognizable chess concepts are encoded by individual layers + channels in AZ Network
   - Can be found using supervised \& unsupervised techniques

---

# Limitations

1. Challenges for Concept Probing (previous section)
2. Knowledge acquisition is not complete --- only a very small part of the model
3. Interpreting ``Feature Detectors'' found via Unsupervised techniques
   - Inherently subjective
4. No Causal Insight into the Learned Concepts (only correlation)

---

# Future Research Areas

1. Addressing Concept Probing Limitations (more than sparse LR)
2. Can we go beyond finding human knowledge embedded in AlphaZero and understand what new concepts are learned?
   - Further analysis of feature detectors found with unsupervised techniques
3. How do we generalize these findings to other machine learning settings? Could we find human-recognizable concepts in different trained models?

---

# Discussion (Paper 1)

- How is this paper different from methods we discussed so far?
  - In terms of goals / techniques / specific models to explain
- Does this approach have potential applications for a more general setting other than board games?
  - What are the pros/cons of using games/artificial environments as baseline for evaluating RL algorithms?
- Can this be applied to our practice of doing science?
  - Can we recreate or extract theories using similar frameworks (AI for science)?
- How should we define or understand ``knowledge'' in these settings?
- How convinced are you about the ``interpretability'' aspect?
  - Who would be potential audience that could benefit from such analysis?

---

# Paper 2

\begin{center}
\includegraphics[width=0.82\columnwidth]{imgs/paper2.png}
\end{center}

[@tang2023daam]

---

# Outline

\begin{columns}
\begin{column}{0.48\textwidth}
\begin{itemize}
\item Related work
\item Background: Stable Diffusion
\item \textbf{DAAM} for text-to-image \textbf{attribution}
\item Results
  \begin{itemize}
  \item Attribution quality analysis
  \item Syntax to pixels
  \item Entanglement
  \end{itemize}
\item Limitations \& discussion
\end{itemize}
\begin{block}{}
DAAM estimates per-pixel attribution for each word in a prompt (post-hoc)
\end{block}
\end{column}
\begin{column}{0.48\textwidth}
\includegraphics[width=\columnwidth]{imgs/daam_intro.png}
\end{column}
\end{columns}

---

# Related Work

\begin{block}{}
This work applies existing techniques (cross-attention) to an \textbf{open source, SOTA} diffusion model to \underline{probe limitations}
\end{block}

- \textbf{Textual perturbation} (Wallace et al., 2019), Attentional Visualization, information bottlenecks to relate important input tokens to outputs of large LMs
- Probing \textbf{vision transformers for verb understanding} (Hendricks \& Nematzadeh, 2021)
- Enhancing diffusion models using \textbf{prompt engineering} (Hertz et al., 2020; Woolf, 2020)
- \textbf{Disentangling} e.g., style and spelling (Karras et al., 2019; Materzynska et al., 2022)
- Counterfactual explanations for \textit{text classification} (Jacovi et al., 2021) --- unclear how to extend to image generation

---

# Background: Generative Models

:::: columns
::: {.column width="45%"}

\begin{center}
\includegraphics[width=\columnwidth]{imgs/generative_models_comparison.png}
\end{center}

:::
::: {.column width="53%"}

- **GAN:** a discriminator and generator compete — the generator learns to
  produce images indistinguishable from real ones.

- **VAE:** an encoder maps the image to a latent vector $\mathbf{z}$,
  a decoder reconstructs it — new images are generated by sampling $\mathbf{z}$.

- **Flow-based:** an invertible transformation $f$ maps images to noise —
  generation runs $f^{-1}$ with no information loss.

- **Diffusion:** gradually destroys an image with Gaussian noise,
  then learns to reverse the process step by step from pure noise.

:::
::::

---

# Background: Image Generation with Diffusion

:::: columns
::: {.column width="40%"}

\begin{center}
\includegraphics[width=\columnwidth]{imgs/image_generation_unet.png}
\end{center}

:::
::: {.column width="58%"}

The U-Net receives a noisy image and a text condition (e.g. "7"), predicts the noise component, and subtracts it — leaving a slightly cleaner image. Repeating this process across $T$ steps recovers a sharp image from pure noise. DAAM operates inside this loop, reading the cross-attention scores at each step to measure how much each word influences each image region.

:::
::::

---

# Background: UNet Architecture

:::: columns
::: {.column width="40%"}

\begin{center}
\includegraphics[width=\columnwidth]{imgs/unet_architecture.png}
\end{center}


:::
::: {.column width="58%"}

- **Downsampling (left):** progressively reduces spatial resolution while increasing channels — extracts increasingly abstract features
- **Bottleneck (bottom):** lowest resolution, highest abstraction — where global context is processed
- **Upsampling (right):** progressively restores spatial resolution — reconstructs the image
- **Skip connections (gray arrows):** pass fine-grained spatial detail from encoder to decoder at each resolution level
- **Cross-attention layers:** embedded at each level — this is where DAAM reads word–pixel scores to build attribution maps

:::
::::


---

# Background: Forward and Reverse Process

\begin{center}
\includegraphics[width=0.6\columnwidth]{imgs/forward_reverse_process.png}
\end{center}

- **Forward:** a fixed process adds Gaussian noise step by step until the image is destroyed.

- **Reverse:** a network $\epsilon_\theta$ predicts and subtracts the noise at each step — repeating this reconstructs a sharp image from pure noise.

**The key point:** the network does not learn to generate images directly, but to predict the noise — a regression task that is much more stable to train.

---

# Diffusion Model Loss {.fragile}


\begin{center}
\includegraphics[width=0.8\columnwidth]{imgs/ldm_with_latent.png}
\end{center}


---

# Latent Diffusion Model Loss {.fragile}

\begin{center}
\includegraphics[width=0.7\columnwidth]{imgs/ldmloss.png}
\end{center}

Both losses train $\epsilon_\theta$ to predict the added noise $\epsilon$ via squared error. $L_{LDM}$ differs from $L_{DM}$ in one way: it first compresses the image into a latent vector $z_t = \mathcal{E}(x)$, making denoising computationally cheaper — this is the key idea behind Stable Diffusion.

---

# Stable Diffusion Architecture

\begin{center}
\includegraphics[width=0.7\columnwidth]{imgs/sd_architecture.png}
\end{center}

Stable Diffusion conditions the denoising process on text by encoding the prompt into word vectors using CLIP — a model pretrained to align text and images in a shared latent space. These vectors are injected into the U-Net via **cross-attention** at every layer, steering noise removal toward regions that are semantically consistent with each word in the prompt.

---

# Cross-Attention for Text Conditioning {.fragile}

\begin{center}
\includegraphics[width=0.7\columnwidth]{imgs/scross_att_sc.png}
\end{center}


Cross-attention links image regions to prompt words: **queries** come from the U-Net's spatial representations, while **keys and values** come from the CLIP-encoded prompt. The softmax scores measure how much each image region attends to each word — and these are precisely the scores DAAM aggregates across layers and timesteps to produce per-word attribution maps.

---

# DAAM: Per-Word Heatmaps

\begin{center}
\includegraphics[width=0.82\columnwidth]{imgs/daam_heatmaps_method.png}
\end{center}


---

# Results: Attribution Analysis Part 1 --- Object Attribution

We can evaluate DAAM as an image-segmentation tool

\begin{center}
\includegraphics[width=0.70\columnwidth]{imgs/object_attribution.png}
\end{center}

---

# DAAM Segments Stable Diffusion Images

\begin{columns}
\begin{column}{0.40\textwidth}
\includegraphics[width=\columnwidth]{imgs/segmentation_table.png}
\end{column}
\begin{column}{0.58\textwidth}

\begin{center}
\includegraphics[width=.35\columnwidth]{imgs/iou.png}
\end{center}

\begin{itemize}
    \item \textbf{Metric:} mIoU --- measures overlap between predicted and ground-truth masks; higher is better
    \item \textbf{mIoU$^{80}$:} restricted to COCO's 80 classes --- favors supervised methods trained on them
    \item \textbf{mIoU$^{\infty}$:} open vocabulary --- any word in the prompt; favors DAAM
    \item \textbf{Key result:} DAAM outperforms all unsupervised baselines by $\sim$30 points and approaches supervised methods on mIoU$^{\infty}$ --- without being trained to segment anything
\end{itemize}
\end{column}
\end{columns}


---

# Results: Attribution Analysis Part 2 --- Generalized Attribution

DAAM can segment \textbf{beyond nouns}:

\begin{center}
\includegraphics[width=0.82\columnwidth]{imgs/generalized_attribution.png}
\end{center}

---

# Evaluating DAAM with a User Study

**Setup:** 50 annotators rated DAAM maps on a 5-point scale (Poor → Excellent),
with 3 raters per image. Abstract words and poor-quality images were discarded.

- 50 annotators, none seeing more than 18% of images
- Every image rated by 3 independent annotators
- Abstract words (e.g. "the") and poor images excluded from evaluation

## Key Insight
DAAM is evaluated beyond nouns — the study covers all interpretable
parts of speech, since words like "running" or "blue" have no
ground-truth segmentation mask.

----

# Evaluating DAAM with a User Study Results 1/2

\begin{center}
\includegraphics[width=.5\columnwidth]{imgs/user_study_results.png}
\end{center}

- **Nouns, verbs, adjectives, proper nouns:** mean opinion score close to *Good*
- **Numerals and adverbs:** closer to *Fair* — harder to localize visually
- **All categories:** >80% of ratings are Fair-to-Excellent

--- 

# Evaluating DAAM with a User Study Results 2/2

\begin{center}
\includegraphics[width=.6\columnwidth]{imgs/user_study2_results.png}
\end{center}

- Lower scores in some categories reflect **word abstractness or generation
failures** — not DAAM failures. When the image and word are interpretable,
- DAAM produces plausible attribution maps across all parts of speech.


---

# Visuosyntactic Analysis

\begin{block}{Key Question}
How does \textbf{syntax} relate to the generated \textbf{pixels}?
\end{block}

- Study \textbf{head-dependent pairs} in sentences using Universal Dependencies (UD) syntax
- For each dependency relation, measure how much the attribution map of the \textit{dependent} overlaps with that of the \textit{head}
- 3 measures: mIoU, mIoD (Mean IoD over the \textbf{D}ependent), mIoH (Mean IoD over the \textbf{H}ead)

---

# Visuosyntactic Analysis: Head-Dependent Pairs

\begin{center}
\includegraphics[width=0.80\columnwidth]{imgs/visuosyntactic_head_dependent.png}
\end{center}


---

# Visuosyntactic Analysis: Measures of Overlap

To compare two DAAM maps (e.g. "blue" vs. "teapot"), three overlap metrics:

:::: columns
::: {.column width="30%"}
\includegraphics[width=\columnwidth]{imgs/measures_of_overlap.png}
:::
::: {.column width="65%"}

- **mIoU** — $|A \cap B| / |A \cup B|$: overall similarity between the two maps
- **mIoD** — $|A \cap B| / |A|$: how much of the **dependent's** map is covered by the head
- **mIoH** — $|A \cap B| / |B|$: how much of the **head's** map is covered by the dependent

**Dominance** is measured by the difference $\Delta = \text{mIoD} - \text{mIoH}$:

- $\text{mIoD} > \text{mIoH}$ $(\Delta > 0)$: the **head dominates** —
  the dependent's map is contained within the head's
- $\text{mIoD} < \text{mIoH}$ $(\Delta < 0)$: the **dependent dominates** —
  the head's map is contained within the dependent's
:::
::::

## In plain English
In "blue teapot": does "blue" attend to the same region as "teapot",
or does it spread beyond it? $\Delta$ tells you which word's
visual footprint contains the other's.

---

# Visuosyntactic Analysis: Results Compound

\begin{center}
\includegraphics[width=\columnwidth]{imgs/vsa_compund.png}
\end{center}

---

# Visuosyntactic Analysis: Results Punctuation & Articles

\begin{center}
\includegraphics[width=\columnwidth]{imgs/vsa_pandart.png}
\end{center}

---

# Visuosyntactic Analysis: Results Verb & Subject/Object


\begin{center}
\includegraphics[width=\columnwidth]{imgs/vsa_verbobject.png}
\end{center}

---

# Visuosyntactic Analysis: Results Verb & Subject/Object

\begin{center}
\includegraphics[width=\columnwidth]{imgs/vs_nom.png}
\end{center}


---

# Visuosyntactic Analysis: Results Adjectives

\begin{center}
\includegraphics[width=\columnwidth]{imgs/vs_adjectives.png}
\end{center}


---

# Visuosyntactic Analysis: Key Takeaways

- \textbf{Noun Compounds} (*"ice cream"*): No dominance --- complement one another
- \textbf{Punctuation \& Articles} (*the donut*): No dominance --- little semantic meaning
- \textbf{Verb \& Subject} (\textit{bird stands}): Head verb \textbf{dominates} --- contextualises subject/object in surroundings (\textit{semi-intuitive})
- \textbf{Nominal Dependents} (*pile of oranges*): Head dominates --- \textit{intuitive}
- \textbf{Adjective Modifiers} (*wooden bench*): Dependent \textbf{dominates} --- \textit{counter-intuitive}

\begin{alertblock}{Summary}
Attribution map of the \textbf{dependent subsumes} that of the head, and vice versa for others. Dominance is intuitive in some cases but \textbf{counter-intuitive} in others.
\end{alertblock}

---

# Visuosemantic Analysis: Cohyponym Entanglement

\begin{block}{Key Question}
Do semantically \textbf{similar words} have \textbf{worse} generation quality?
\end{block}

**Prompt structure:** `"a(n) <noun> and a(n) <noun>"`

- **Cohyponym example:** `"a giraffe and a zebra"` —
  both nouns belong to the same semantic category (animals)

- **Non-cohyponym example:** `"a zebra and a fridge"` —
  nouns from different semantic categories

## In plain English
Cohyponyms are words that share a common hypernym — "giraffe" and "zebra"
are both animals. The hypothesis is that Stable Diffusion struggles to
generate *both* objects when they are semantically similar,
because their DAAM maps tend to overlap and entangle.

---

# Cohyponym Entanglement: Results 

:::  columns
::: {.column width="60%"}
**Prompt:** `"a giraffe and a zebra"`

- Stable Diffusion generation **worsens** with cohyponyms —
  it generates **one** noun but **not both**
- The DAAM maps of the two nouns **overlap strongly**,
  revealing **feature entanglement**: the model cannot
  separate two semantically similar objects in pixel space

::::

::: {.column width="40%"}

\includegraphics[width=\columnwidth]{imgs/cohyponym_results.png}

::::
:::

## Key Insight
DAAM doesn't just show that generation fails —
it explains *why*: when two words attend to the same image regions,
the model cannot allocate distinct visual space to each object.
Entangled maps are both a symptom and a diagnosis.

---

# Cohyponym Entanglement: Non-Cohyponym Contrast

::: columns
::: {.column width="60%"}
**Prompt:** `"a zebra and a fridge"`

- Stable Diffusion generates **both** nouns successfully
- The DAAM maps of the two nouns are **distinct** —
  each word attends to a separate, non-overlapping region


::::

::: {.column width="40%"}
\includegraphics[width=\columnwidth]{imgs/nooncohyponym_results.png}

::::
:::

## Key Insight
The contrast with the cohyponym case is stark: when two objects
are semantically distant, the model allocates **independent visual
space** to each — no entanglement, no generation failure.
This confirms that overlap in DAAM maps is not accidental
but reflects a genuine failure mode of the model.

---

# Visuosemantic Analysis: Adjectival Entanglement

**Prompt structure:** `"<adj> <noun> <verb phrase>"`

- **Examples:**
  - `"a [rusty] shovel sitting in a clean shed"`
  - `"a [bumpy] ball rolling down a hill"`

**Expected behavior:** if there is **no entanglement**, varying the adjective
should only affect the noun's region — the background should
**not gain attributes** pertaining to that adjective.

## In plain English
If "rusty" only modifies the shovel, swapping it for "shiny"
should change the shovel but leave the shed untouched.
If the entire image changes, the adjective has leaked
beyond its intended target — a sign of feature entanglement.

---


# Adjectival Entanglement: Results

::: columns
::: {.column width="50%"}
\includegraphics[width=\columnwidth]{imgs/adjectival_results.png}

::::

::: {.column width="50%"}

Attribution maps for adjectives attend **too broadly** —
far beyond the noun they modify.
Changing the adjective (rusty → metallic → wooden)
alters the **entire scene**, not just the noun.
This is **feature entanglement**: the adjective's
visual footprint leaks into the background.

::::
:::

## Key Insight
DAAM reveals *why* Stable Diffusion struggles with
precise attribute binding — adjectives do not attend
locally to their noun, but globally to the whole image,
making it impossible to change one attribute
without affecting the rest of the scene.

---

# Summary

- DAAM provides pixel-level attribution maps for Stable Diffusion, a state-of-the-art text-to-image generator
- These maps appear to be informative, as evaluated through segmentation tasks and user study
- DAAM can be a useful tool for further understanding and analyzing Stable Diffusion --- e.g. through visuosyntactic analysis and visuosemantic analysis

---

# Class Discussion

- Does DAAM give a clear understanding about how a large-scale latent diffusion model synthesizes text to image and which parts of an image are influenced the most?
- Does DAAM explain all the dynamics of how images are synthesized? If not, how should DAAM be modified to better explain image generation?
  - Other explanatory tools besides **attention** and **segmentation proposals**?
- DAAM pointed out failure cases of Stable Diffusion. Are there further interpretability methods needed to understand why **feature entanglement** is occurring and how it could be improved?

---

\begin{center}
\Huge Thank You!
\end{center}

---

# References {.allowframebreaks}

\footnotesize
