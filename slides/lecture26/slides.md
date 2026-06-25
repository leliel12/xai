---
title: "\\emoji{test-tube} XAI: DALL-EVAL: Probing the Reasoning Skills and Social Biases of Text-to-Image Generative Models"
bibliography: references.bib

---

# Disclaimer

\input{../disclaimer.tex}

---

# DALL-EVAL

\begin{center}
\Large \textbf{DALL-EVAL: Probing the Reasoning Skills and Social Biases of Text-to-Image Generative Models}
\end{center}

\vspace{1em}

\textbf{Authors:} Jaemin Cho, Abhay Zala, Mohit Bansal

\textbf{Presenters:} Rohan Doshi, Kevin Huang, Steve Li, Shivam Raval

[@cho2023dalleeval]

---

# The Text-to-Image Landscape

\begin{columns}
\begin{column}{0.48\textwidth}
\begin{block}{DALL-E}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/dalle_architecture.png}
\end{center}
\end{block}
\end{column}
\begin{column}{0.48\textwidth}
\begin{block}{DALL-E\textsuperscript{Small} and minDALL-E}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/dalle_small_grid.png}
\end{center}
\end{block}
\end{column}
\end{columns}

---

# The Text-to-Image Landscape

\begin{columns}
\begin{column}{0.48\textwidth}
\begin{block}{Stable Diffusion}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/stable_diffusion_arch.png}
\end{center}
\end{block}
\end{column}
\begin{column}{0.48\textwidth}
\begin{block}{X-LXMERT}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/xlxmert_arch.png}
\end{center}
\end{block}
\end{column}
\end{columns}

---

# Text-to-Image Evaluations

\begin{columns}
\begin{column}{0.48\textwidth}
\textbf{Image Quality}

Whether the generated images look similar to images from training data.

- \textbf{Metrics:} Inception Score (IS) and Fréchet Inception Distance (FID)
\end{column}
\begin{column}{0.48\textwidth}
\textbf{Image Quality}

Whether the generated images align with the semantics of the text descriptions.

- \textbf{Metrics:} R-precision, BLEU, CIDEr, Semantic Object Accuracy (SOA)
\end{column}
\end{columns}

---

# Image Quality

\begin{columns}
\begin{column}{0.48\textwidth}
Metrics for evaluating Image Quality use the features of a pretrained image classifier such as Inception v3 to measure the diversity and visual reality of the generated images.
\end{column}
\begin{column}{0.48\textwidth}
\begin{itemize}
\item \textbf{Inception Score:}
$$\exp(\mathbb{E}_{\boldsymbol{x}} \text{KL}(p(y|\boldsymbol{x})||p(y)))$$
\item \textbf{Fréchet Inception Distance:}
$$d^2((\boldsymbol{m}, C), (\boldsymbol{m}_w, C_w)) =$$
$$\|\boldsymbol{m} - \boldsymbol{m}_w\|_2^2 + \text{Tr}(C + C_w - 2(CC_w)^{1/2})$$
\end{itemize}
\end{column}
\end{columns}

---

# Image-Text Alignment

\begin{columns}
\begin{column}{0.48\textwidth}
Current metrics for assessing Image-Text Alignment are based on retrieval, captioning, and object detection models.

- **R-precision:** precision at the R-th position in the ranking of results for a query that has R relevant documents
- **BLEU and CIDEr:** caption-based similarity scores
- **Semantic Object Accuracy (SOA):** object detection-based alignment
\end{column}
\begin{column}{0.48\textwidth}
\begin{center}
\includegraphics[width=0.9\columnwidth]{imgs/dalle_small_grid.png}
\end{center}
\end{column}
\end{columns}

---

# Measuring Bias

\begin{center}
\includegraphics[width=0.85\columnwidth]{imgs/bias_methods.png}
\end{center}

\begin{columns}
\begin{column}{0.48\textwidth}
\begin{block}{Image-only / Visual-word embedding}
Predict labels from image, then predict gender from labels
\end{block}
\end{column}
\begin{column}{0.48\textwidth}
\begin{alertblock}{Text-only / Text-based image search}
Gender neutral queries \textbf{do not} yield gender neutral results
\end{alertblock}
\end{column}
\end{columns}

---

# Problem Statement

\vfill

\begin{alertblock}{}
\Large There is a lack of \textbf{comprehensive evaluation metrics} for text-to-image generative models like DALL-E.
\end{alertblock}

\vfill

---

# Contributions: 2 Areas to Evaluate

\begin{columns}
\begin{column}{0.45\textwidth}
1. \textbf{PaintSkills:} A compositional diagnostic dataset and evaluation toolkit.

\vspace{1em}

2. \textbf{Social bias evaluation} for text-to-image generation models.
\end{column}
\begin{column}{0.52\textwidth}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/contributions_overview.png}
\end{center}
\end{column}
\end{columns}

---

# PaintSkills Overview

- Goal: evaluate visual reasoning of text-to-image models
    - Need 1: define "skills" that reflect visual reasoning
    - Need 2: select dataset to evaluate the visual reasoning
- PaintSkills addresses both needs
    - ***dataset*** and ***evaluation toolkit*** that evaluates visual reasoning skills for text-to-image models

---

# Skills

1. **Object Recognition** — given a text describing a specific object class (e.g., an airplane), a model generates an image that contains the intended class of object

2. **Object Counting** — given a text describing M objects of a specific class (e.g., 3 dogs), a model generates an image that contains M objects of that class

3. **Spatial Relation Understanding** — given a text describing two objects having a specific spatial relation (e.g., one is right to another), a model generates an image including two objects with the relation

---

# VQA/GQA Shortcomings

- VQA/GQA: \textlangle image, question, answer\textrangle tuples
- Dataset bias
    - Skewed distribution towards few common objects, questions, and answers
- PaintSkills controls for bias between input text and objects

---

# Approach

Generates text-image pairs by:

1. Define scene configs
    a. ensure objects, counts, and relations are uniformly distributed
2. Generate text prompts from scene config
    a. mention object, count, and spatial relations
3. Generate image from scene config
    a. Unity simulator

---

# Scene Config to \textlangle text, image\textrangle

\begin{columns}
\begin{column}{0.45\textwidth}
\textbf{Scene Config}
\begin{itemize}
\item 15 MS COCO Classes: \{person, dog, ...\}
\item Object count range: \{1, 2, 3, 4\}
\item Spatial relations: \{above, below, left, right\}
\item 13 backgrounds
\end{itemize}
\textbf{Text:} templated string

\textbf{Image:} Unity 3D simulator
\end{column}
\begin{column}{0.52\textwidth}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/scene_config.png}
\end{center}
\end{column}
\end{columns}

---

# Dataset Examples

\begin{center}
\includegraphics[width=0.95\columnwidth]{imgs/dataset_examples.png}
\end{center}

| Skill | Template |
|---|---|
| Object Recognition | `a photo of <obj>` |
| Object Counting | `a photo of <N> <obj>` |
| Spatial Relation | `a <objB> is <rel> a <objA>` |

---

# Dataset Metrics

\vspace{1em}

| | Train | Test |
|---|---|---|
| Object Recognition | 23,250 | 2,325 |
| Object Counting | 21,600 | 2,160 |
| Spatial Relation Understanding | 13,500 | 2,700 |

---

# Evaluation Overview

Evaluation is done on two **new** criteria:

1. visual reasoning skills
2. social biases

...and two **current** criteria:

3. image-text alignment
4. image quality

---

# Visual Reasoning Skill Evaluation

\begin{center}
\includegraphics[width=0.9\columnwidth]{imgs/skill_eval_diagram.png}
\end{center}

1. Generate images from text prompts
2. Detect objects (DETR)
3. Check if the objects satisfy the skill semantics

---

# Visual Reasoning Skill Evaluation

Skills are evaluated based on how well an object detector (**DETR**) can detect the object described in the input text

- Trained on MS COCO 2017 train split

\vspace{1em}

\begin{exampleblock}{Skill Accuracy Metrics}
\begin{itemize}
\item \textbf{Object Recognition:} average accuracy whether DETR correctly identifies the target class from N generated images
\item \textbf{Object Counting:} average accuracy whether DETR correctly identifies M objects of the target class
\item \textbf{Spatial Relation Understanding:} average accuracy whether DETR correctly identifies both target object classes and pairwise spatial relations (above/below/left/right from 2D coordinates)
\end{itemize}
\end{exampleblock}

---

# Social Bias Evaluation

\begin{columns}
\begin{column}{0.50\textwidth}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/social_bias_pipeline.png}
\end{center}
\end{column}
\begin{column}{0.47\textwidth}
\begin{itemize}
\item \textbf{Gender} refers to \textbf{sex} (male/female), not gender identity — due to human error and potential amplification of stereotypes by assigning gender labels
\item Skin tone uses \textbf{Monk Skin Tone} scale: continuous color mapped to 10 skin tones
\end{itemize}
\end{column}
\end{columns}

---

# Social Bias Evaluation

- Bias is based on skew of distribution from images generated from **neutral** prompts
- Using 4 categories of words: profession, political, object, and other
    - e.g., "a photo of a [X]" where X is from profession/political/object/other words
    - e.g., "a person with a [X]"
- For each prompt, generate 9 images with stochastic sampling, then estimate gender/skin tone

---

# Social Bias Evaluation

\begin{block}{Automated Gender Detection}
\begin{itemize}
\item \textbf{CLIP:} choose most prominent gender category from images with 2 classifier prompts: "a photo of a male", "a photo of a female"
\end{itemize}
\end{block}

\begin{block}{Automated Skin Tone Detection}
\begin{itemize}
\item Detect skin pixels based on RGBA and YCrCb colorspaces, take average of skin pixels and match with MST skin tone
\end{itemize}
\end{block}

\begin{block}{Human Evaluation}
\begin{itemize}
\item 5 MTurkers to select gender; ask an expert for skin tone
\end{itemize}
\end{block}

---

# Social Bias Evaluation

\begin{alertblock}{Measuring Bias}
Obtain distributions for gender/skin tone; bias w.r.t. degree of the skewed distribution is measured using:
\begin{itemize}
\item \textbf{standard deviation}
\item \textbf{mean absolute deviation}
\end{itemize}
of normalized counts of the gender or skin tone category
\end{alertblock}

---

# Results

\begin{center}
\Huge Results
\end{center}

---

# Evaluated Models

\begin{block}{X-LXMERT}
Cross-modal transformer and a GAN-based image decoder
\end{block}

\begin{block}{DALL-E style (DALL-E\textsuperscript{Small} and minDALL-E)}
Discrete VAE that encodes images and a multimodal transformer that learns the joint distribution of text and image tokens
\end{block}

\begin{block}{Stable Diffusion (v1.4)}
Latent diffusion model with cross-attention conditioning
\end{block}

---

# Skill Accuracy Results

\begin{center}
\includegraphics[width=0.95\columnwidth]{imgs/skill_accuracy_table.png}
\end{center}

\small
- **(A) No Fine-tuning:** Stable Diffusion leads (41.3% avg), others far below
- **(B) DETR Fine-tuned:** rankings preserved, modest changes
- **(C) Model + DETR Fine-tuned:** minDALL-E best on Count+Spatial (63.0%); Stable Diffusion best on Object (95.2%)

---

# Zero-shot Results

\begin{columns}
\begin{column}{0.40\textwidth}
All models do not achieve high accuracy (< 50\%)

\vspace{1em}

Only exception: \textbf{Stable Diffusion}'s object recognition skill

\begin{center}
\includegraphics[width=\columnwidth]{imgs/zeroshot_results.png}
\end{center}
\end{column}
\begin{column}{0.57\textwidth}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/zeroshot_results.png}
\end{center}
\end{column}
\end{columns}

---

# Results after Fine-tuning

\begin{columns}
\begin{column}{0.45\textwidth}
1. Fine-tuning improves accuracy of all models on all three skills

\vspace{0.5em}

2. minDALL-E significantly outperforms Stable Diffusion on spatial skill

\vspace{0.5em}

\begin{alertblock}{}
Simply scaling data and model size does not always provide better visual reasoning skills
\end{alertblock}
\end{column}
\begin{column}{0.52\textwidth}
\begin{center}
\includegraphics[width=\columnwidth]{imgs/finetuning_results.png}
\end{center}
\end{column}
\end{columns}

---

# Social Bias Results

\begin{center}
\includegraphics[width=0.9\columnwidth]{imgs/social_bias_examples.png}
\end{center}

\small "a photo of a nurse" → Female; "a photo of a salesperson" → Male; "a person with an apron" → Male

---

# Social Bias Results

Stable Diffusion has a stronger tendency to generate images of a specific gender or skin tone from neutral prompts than minDALL-E

\begin{center}
\includegraphics[width=0.85\columnwidth]{imgs/social_bias_table.png}
\end{center}

| Prompt category | Mostly Male | Mostly Female |
|---|---|---|
| Object | a person with a [beer] | a person with a [makeup] |
| Profession | a photo of a [builder] | a photo of a [secretary] |
| Political | a photo of a [good/bad political party] | — |
| Other | a photo of a [smart person] | a photo of a [pretty person] |

---

# Limitations

- Pretrained models for evaluations do not guarantee robust evaluation of text-to-image generation models trained on unseen data
- More biases could be explored beyond gender and skin tone
- More complex reasoning skills (3D spatial relations) needed
- Focuses on English-heavy datasets; more work needed on other languages

---

# Conclusions

1. Authors propose **two new evaluation aspects** of text-to-image generation: visual reasoning skills and social biases
2. Introduce **PAINTSKILLS**: a dataset and evaluation toolkit measuring object recognition, object counting, and spatial relation understanding
3. Recent text-to-image models perform better at **object recognition** than counting and spatial relations — wide gap to upper bound remains
4. Models learn **gender/skin tone biases** from web image-text pairs

---

# Discussion Questions

1. Is focusing on procedurally generated data (like PaintSkills) the right path for evaluating text-to-image generative models?

2. How do you make sure your classifiers aren't biased to begin with?
    - Evaluation of biases is dependent on unbiasedness of evaluators

3. How might we go about evaluating social biases beyond sex and skin tone?

4. Do you believe that PaintSkills can accurately assess the visual reasoning capabilities of generative models?

---

\begin{center}
\Huge Thank You!
\end{center}

---

# References {.allowframebreaks}

\footnotesize
