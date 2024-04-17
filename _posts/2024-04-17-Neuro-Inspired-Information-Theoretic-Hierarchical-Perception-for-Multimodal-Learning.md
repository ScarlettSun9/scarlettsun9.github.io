---
layout: distill
title: Paper Review 📝 Neuro-Inspired ITHP for Multimodal Learning
description: Neuro-Inspired Information-Theoretic Hierarchical Perception for Multimodal Learning - a new method for multimodal representation fusion
tags: AI multimodal
giscus_comments: true
date: 2024-04-17
featured: true


# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: Approach & Model
    # if a section has subsections, you can add them as follows:
    # subsections:
    #   - name: Example Child Subsection 1
    #   - name: Example Child Subsection 2
  - name: Niche
  - name: Concepts
  - name: Cognitive answer for multimodal integration
  - name: Information Bottleneck (IB)
  - name: Model Architecture
  - name: Train the Model
  - name: Experiment
  - name: Sarcasm Detection
  - name: Varying Lagrange multiplier
  - name: Sentiment Analysis
  - name: Personal Comment

# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }
---

[Link of the paper](https://arxiv.org/abs/2404.09403)

This paper demonstrates a method for fusing multimodal information with hierarchical information path way and information bottleneck structure. Experimental results show that the new method can outperform state-of-the-art benchmarks on sarcasm detection and sentiment analysis.

## Approach & Model

### Niche

Multimodal Fusion can be generally classified into two types: early fusion and late fusion. Early fusion underscores cross-modal information interaction. Common methods inlcude concatenating representations directly and adding representations together (which prerequisite the alignment among different modality). Yet the amount and importance of information differ across various modalities. One modality may play a much more important role in some context. Also for some people, they are more "visual" or "audio" when they get information than simply reading from text. These are cognitive features when humans processing information. So how to incorporate these to AI's learning? The authors provide their tentative answer: ITHP.

### Concepts

#### Cognitive answer for multimodal integration

* Information from different modalities forms connections in a specific order within the brain.
* Synaptic connections between different modality-receptive areas are reciprocal. This reciprocity enables information from various modalities to reciprocally exert feedback.
* Such a sequential and hierarchical processing approach allows the brain to start by processing information in a certain modality, gradually linking and processing information in other modalities, and finally analyzing and integrating information effectively in a coordinated manner.
* The brain selectively attends to relevant cues from each modality, extracts meaningful features, and combines them to form a more comprehensive and coherent representation of the multimodal stimulus.

#### Information Bottleneck (IB) 
* This is mainly a tradeoff between compression and prediction in an information flow.
* Target: find a **compact representation of one given state** while **preserving its predictive power with respect to another state**.

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-1.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    An illustration of IB implementation in multimodal representation learning.
  </div>
</div>

This is a simple illustration for the Information Bottleneck structure for multimodal fusion. $$X_0$$, $$X_1$$ and $$X_2$$ are the representations (matrices) of three modalities, from the most important (prime modality) to the least (remaining modalites). $$B_0$$, $$B_1$$ are two latent states, or so-called "bottleneck". According to the IB theroy, $$B_0$$ should compact the information of $$X_0$$ and at the same time maximize the relevant information of $$X_1$$. In this way, $$B_0$$ can be treated as the fusion of the first two modalities. Based on $$B_0$$, $$B_1$$ should similarly compact the information of $$B_0$$ (the first two modalities) and preserve the information of $$X_2$$.

In this way, $$B_2$$ is the final version of modality fusion. It is then used to predict the result $$Y$$.

To achieve the tradeoff between compressing a state X into the latent state B and maintaining relevant information of another state Y, the loss function can be like this ($$I$$ is the mutual information):

$$
L = I(X;B)-\beta I(B;Y)
$$

### Model Architecture

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-2.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    An illustration of the IHTP model structure.
  </div>
</div>

First, to further explain the bottleneck structure loss function, we take a look at the latent state $$B_0$$. Its optimization should subject to the following 3 equations:

$$
I(X_0; X_1) − I(B_0; X_1) \leq \epsilon_1
$$

Hidden state $$B_0$$ covers as much relevant information in $$X_1$$ as possible, so $$I(X_0; X_1) − \epsilon_1$$ is a lower bound of $$I(B_0; X_1)$$;

$$
I(B_0; B_1) \leq \epsilon_2
$$

Minimize $$I(B_0; B_1)$$ to further compress the information of $$B0$$ into $$B1$$;

$$
I(X_0; X_2) − I(B_1; X_2) \leq \epsilon_3
$$

$$B_1$$ is constructed to retains as much relevant information in $$X_2$$ as possible, with $$I (X_0 ; X_2) − \epsilon_3$$ as a lower bound.

The general loss function (a Lagrangian function) is:

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-3.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

This can minimize the mutual information $$I(X_0;B_0)$$ and $$I(B_0; B_1)$$, and maximize the mutual information $$I(B_0; X_1)$$ and $$I(B_1; X_2)$$.

Further, let's see the detailed loss of the two bottlenecks: $$X_0$$-$$B_0$$-$$X_1$$ and $$B_0$$-$$B_1$$-$$X_2$$. The below equations are just an expansion of the above loss function. It considers deterministic distribution as $$p$$ and the random probability distribution fitted by the neural network as $$q$$.

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-4.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

Here, *KL(p(X)||q(X))* is the Kullback-Leibler (KL) divergence between the two distributions *p(X)* and *q(X)*.

### Train the Model

Loss functions again! The overall loss for the two-level hierarchy (three modality):

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-5.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

If we need to train for the specific downstream task, the function is:

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-6.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

## Experiment

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-7.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    Information flow illustration on sarcasm detection task (as well as sentiment analysis task). 
  </div>
</div>

### Sarcasm Detection

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-8.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

* Dataset: the Multimodal Sarcasm Detection Dataset (MUStARD)
* Model to compare: fine-tuned multimodal sarcasm detection model (“MSDM”) by Castro et al. (2019).
* Modality order: Visual - Text - Audio (Considering the binary classification results across the three modalities and taking into account the size of the embedding features for each modality (dv = 2048, dt = 768, da = 283))
* Results:
  * For single modality, the two methods' effects are almost the same.
  * For two modalities, V-T is the best version.
    * MSDM V-A lower than V ➡️ struggled to effectively extract meaningful information by combining the embedding features from video and audio.
  * For three modalities, ITHP outperforms MSDM ➡️ succeeds to construct the effective information flow among the multimodality states for the sarcasm detection task.

#### Varying Lagrange multiplier

The authors also adjust the Lagrange multipliers, $$\gamma$$ and $$\beta$$, to see its influence on the precision and recall value.

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-9.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

The upper right triangular part is brighter than the lower left triangular part ➡️ the model benefits more from a higher β than a higher γ ➡️ retaining more relevant information from Text (X1) is more important for sarcasm detection.

### Sentiment Analysis

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-10.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-11.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>

* Dataset: CMU-MOSI and CMU-MOSEI
* Models to compare: see the above tables
* Modality order: Text - Audio - Video (with em- bedding feature sizes of dt = 768, da = 74, and dv = 47 for text, audio, and video, re- spectively)
* Results:
  * ITHP model outperforms all the SOTA models in both BERT and DeBERTa incorporation settings.
  * Significant strides in per-formance were observed with the integration of DeBERTa with the ITHP model, surpassing even human levels.

## Personal Comment

For me the ITHP model is important since I have used multimodal LLMs for emotion classification. In my own experiment I just followed the traditional way to concatenate two modalities' representations together, which works but not works well enough. I thought the fusion is a potential problem and I directly change it to late fusion. But this information bottleneck is a good structure to integrate. But again, how does it compare to the late fusion? Sometimes I even doubt the early fusion in theory. Is that procedures really useful or just our innocent manipulation of data and models? This always reminds me two theory: one is the allegory of the cave by Plato. All the models, when we don't really know what happened inside, are shadows projected on to the wall by the real INTELLIGENCE; Another seems more optimistic: It doesn't matter whether a cat is black or white, as long as it catches mice. Now most models have successfully caught the mice, including this ITHP.

BTY, this is my first post!!! And it takes almost the same time to write a paper summary/review than read the paper! 😂