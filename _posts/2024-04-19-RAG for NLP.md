---
layout: distill
title: Paper Review 📝 - Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks
description: RAG original paper in 2020 (OMG It's been 4 years...)
tags: RAG NLP
giscus_comments: true
date: 2024-04-19
featured: false


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
    subsections:
      - name: Niche
      - name: Model
      subsections:
        - name: RAG-Sequence Model
        - name: RAG-Token Model
      - name: Retriever: DPR
      - name: Generator: BART
  - name: Experiment
  - name: Impact
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

Paper name: Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks


[Link of the paper](https://arxiv.org/abs/2404.09403)

This paper shows a method to combine pretrained language model with memory on specific knowledge. To tackle the hallucination problems of the language model and lack of knowledge update, the authors bring hybrid parametric (pretrained model) and non-parametric memory (retriever document index) to today's seq2seq models. This kind of RAG model is suitable for the knowledge-intensive tasks and has achieved state-of-the-art results on many extractive QA datasets and knowledge-generation tasks. The model is also flexible to change the domain knowledge to update itself. It is a good way to "inject" domain knowledge to the general model and enhance the quality and safety of the generated content.

## Approach & Model

### Niche

* Pre-trained neural language models 
  * cannot easily expand or revise their memory;
  * can't straightforwardly provide insight into the predictions;
  * may produce "hallucinations"
* Hybrid models (parametric memory + non-parametric memories) can address some of these issues, yet have only explored open-domain extractive QA.

### Model

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240419-1.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    RAG approach overview 
  </div>
</div>

Actually it took me a while to clarify the two core models: RAG-Sequece Model & RAG-Token Model. Now let's focus on the formula and explain it step by step.

#### RAG-Sequence Model

$$
p_{RAG-Sequence}(y|x) \approx \sum_{z\in top-k(p(·|x))}p_{eta}(z|x)p_{\theta}(y|x,z)=\sum_{z\in top-k(p(·|x))}p_{\eta}(z|x)\prod^N_i p_{\theta}(y_i|x,z.y_{1:i-1})
$$

First, based on the input question $$x$$, the retriever will find top-k documents that are most related to the input. This step is for $$p_{eta}(z|x)$$.

Next, for each selected document $$z$$, the generator will generate token by token, with the condition of input question $$x$$, selected document $$z$$, and previously generated tokens $$y_{1:i-1}$$. After genrating the whole result, multiplicate all the probabilities here $$p_{\theta}(y_i|x,z.y_{1:i-1})$$ to get the probability of generating the sentence provided the document $$z$$.

Finally, sum the sentence probability for each document up and get the total probability of generating the answer $$y$$ under the condition $$x$$ with the help of the top-k documents.

> The RAG-Sequence model uses the same retrieved document to generate the complete *sequence*.

#### RAG-Token Model

$$
p_{RAG-Token}(y|x)\approx \prod^N_i \sum_{z\in top-k(p(·|x))}p_{\eta}(z|x)p_{\theta}(y_i|x,z,y_{1:i-1})
$$

First, for each top-k document, compute its probability $$p_{\eta}(z|x)$$ with the probability to generate the next token based on question $$x$$, document $$z$$ and previous tokens $$y_{1:i-1}$$. The result is the probability of the document $$z$$ to generate the token $$y$$.

Next is the summation. In this token generation step, summarize all the probabilities of the top-k documents.

Finally, after generating the whole sentence, multiply the probability to generate each single word (the summation value of each step) to get the probability to generate the sentence. 

> In the RAG-Token model we can draw a different latent document for each target token and marginalize accordingly. This allows the generator to choose content from several documents when producing an answer.

### Retriever: DPR

$$
p_{\eta}(z|x) \propto (d(z)^T q(x)) \quad d(z)=BERT_{d(z)}, \, q(x)=BERT_{q(x)}
$$

The retriever uses document encoder $$q(x)$$ and query encoder $$d(z)$$ to get the list of $$k$$ documents $$z$$ with the highest prior probability $$p_{\eta}(z|x)$$, which is a Maximum Inner Product Search (MIPS) problem.

The retriever is pretrained as the *non-parametric memory* of the RAG model.

### Generator: BART

BART-large, a pre-trained seq2seq transformer, is the generator of RAG. Its parameters is the *parametric memory* part.

## Experiment

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240417-7.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    Information flow illustration on sarcasm detection task (as well as sentiment analysis task). 
  </div>
</div>

* 4 tasks: 
  * Open-domain QA
  * Abstractive QA
  * Jeopardy Qeusiton Generation
  * Fact Verification

* Key results:
  * RAG can combines the generation flexibility of the “closed-book” (parametric only) approaches and the performance of "open-book" retrieval-based approaches. 
  * (Jeopardy Questoin Generation shows how) parametric and non-parametric memories work together - the non-parametric component helps to guide the generation, drawing out specific knowledge stored in the parametric memory
  * Generation Diversity: calculate the ratio of distinct ngrams to total ngrams generated by different models and find RAG-Seq's generations are more diverse than RAG-Token and BART.
  * People prefer RAG’s generation over purely parametric BART, finding RAG more factual and specific. 

## Impact

1. **Parametric & Non-parametric memories** Opens up new research directions on how parametric and non-parametric memories interact and how to most effectively combine them.
2. **Factuality**: RAG models are more strongly grounded in real factual knowledge, making it "hallucinate" less with generations that are more factual, and offers more control and interpretability.
3. **Downsides**: RAG can be employed as a language model, and any potential external knowledge source for language model, will probably never be entirely factual and completely devoid of bias. 

> AI systems could be employed to fight against misleading content and automated spam/phishing.


## Personal Comment

RAG, a really smart method to combine real-world knowledge with pretrained language model, can avoid full-finetuning the model and is flexible enough to change domain knowledge. RAG-Sequence and RAG-Token were confusing at first. Even though I have made a step-by-step clarification, it would be better to check the original codes to see how the two models are implemented.

(Okay I find most of the model innovations are smart 😂.)