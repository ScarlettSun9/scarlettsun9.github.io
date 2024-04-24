---
layout: post
title: Paper Review 📝 - Retrieval-Augmented Generation for Large Language Models - A Survey
date: 2024-04-23 11:59:00-0400
description: Paper review for RAG in LLMs | Last revised 27 March 2024
tags: RAG LLM
categories: 
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---

> [!TIP]
> The most important thing I learned from reading this paper is not the technical part of RAG for LLMs, but the best time to read a survey paper. The best practice to get to know a new branch of knowledge is not read survey papers at first (like me this time 😢), but to fully understand several major / key / "milestone" papers first (which can be selected from the references), then come back to read the whole survey paper. 

---

*The survey paper is already a summary. And this post is a summary of summary :D*

## RAG: Niche, Research Stages, and Research Paradigms

### Niche (in today's research of RAGs for LLMs)

LLMs encounter challenges like hallucination, outdated knowledge, and non-transparent, untraceable reasoning processes. ➡️ RAG is a promising solution for it by incorporating knowledge from external databases, which is benefitial to enchance the accuracy and credibility of the generation, continuous knowledge updates and integration of domain-specific information.

### Research Stages

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240423-1.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    Technology tree of RAG research 
  </div>
</div>

* Early stage (mainly before the arrival of ChatGPT): foundational works focusing on enhancing language models by incorporating additional knowledge through Pre- Training Models (PTM).
* Inference stage: providing better information for LLMs to answer more com- plex and knowledge-intensive tasks during the inference
* Today: delving deeper and integrating more with the fine-tuning of LLMs 

### Research Paradigms

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240423-2.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    RAG research paradigms: Naive RAG, Advanced RAG and Modular RAG
  </div>
</div>

The development of Advanced RAG and Modular RAG is a response to these specific shortcomings in Naive RAG.

#### Naive RAG

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240423-3.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    Naive RAG: a representative instance of the RAG process applied to question answering
  </div>
</div>

The process of this Naive RAG in question-answering task is similar to the [original paper of RAG](www.example.org), except for modifying the prompt (combination of query and retrieved chunks). However, the Naive RAG has notable drawbacks:

1. **Retrieval Challenges**: The retrieval phase often struggles with precision and recall, leading to the selection of misaligned or irrelevant chunks, and the missing of crucial information.
2. **Generation Difficulties**: issue of hallucination, irrelevance, toxicity, or bias in the outputs
3. **Augmentation Hurdles**: Integrating retrieved information can result in disjointed or incoherent outputs, repetitive responses.
4. **Over-relyance**: The generation models might overly rely on augmented information, leading to outputs that simply echo retrieved content without adding insightful or synthesized information.

#### Advanced RAG

* Target: enchance retreival quality
* Pre-retrieval process: optimizing the indexing structure and the original query
  * enhancing data granularity
  * optimizing index structures
  * adding metadata
  * alignment optimization
  * mixed retrieval
* Post-retrieval process:
  * rerank chunks - Re-ranking the retrieved information to relocate the most relevant content to the edges of the prompt
  * context compressing - selecting the essential information, emphasizing critical sections, and shortening the context to be processed

#### Modular RAG

* Modular RAG builds upon the foundational principles of Advanced and Naive RAG.
* New modules
  * search module: adapting to spe- cific scenarios & enabling direct searches across various data sources like search engines, databases, and knowledge graphs
  * fusion module: employing a multi-query strategy that expands user queries into diverse perspectives
  * memory module: leveraging the LLM’s memory to guide retrieval & creating an unbounded memory pool
  * route module: navigating through diverse data sources & selecting the optimal pathway for a query
  * predict module: reducing redundancy and noise by generating context directly through the LLM
  * task adapter: tailoring RAG to various downstream tasks & automating prompt retrieval for zero-shot inputs & creating task-specific retrievers through few-shot query generation
* New patterns
  * allowing module substitution or reconfiguration to address specific challenges

## RAG vs. Fine-tuning vs. Prompt Engineering

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240423-4.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    RAG vs. Fine-tuning vs. Prompt Engineering in the aspects of “External Knowledge Required” and “Model Adaption Required”
  </div>
</div>

* RAG, fine-tuning and prompt engineering are all optimization methods for LLMs.

* RAG
  * providing a model with a tailored textbook for information retrieval, ideal for **precise information retrieval** tasks
  * excels in **dynamic environments** by offering real-time knowledge updates and effective utilization of external knowledge sources with **high interpretability**
  * higher latency and ethical considerations regarding data retrieval
* Fine-tuning
  * more static
  * comparable to a student internalizing knowledge over time
  * suitable for scenarios requiring replication of **specific structures, styles, or formats**
  * requiring retraining for updates but enabling **deep customization of the model’s behavior and style**
  * demanding significant computational resources for dataset preparation and training
  * can reduce hallucinations, but may face challenges with unfamiliar data
  * **LLMs struggle to learn new factual information through unsupervised fine- tuning.**
* Prompt engineering
  * leveraging a model’s inherent capabilities with minimum necessity for external knowledge and model adaption

## The R of RAG - Retrieval

* Retrieval Source
  * Data structure
    * unstructured data (e.g. text)
    * semi-structured data (e.g. PDF (which contains text and table))
    * structured data (e.g. knowledge graph)
    * LLM-generated content
  * Retrieval gradularity
    * croase-grained vs. fine-grained
    * Token ➡️ Phrase ➡️ Sentence ➡️ Proposition ➡️ Chunks ➡️ Document
    * Special case: using proposition as retrieval unit
* Indexing optimization
  * Chunking Strategy (large chunk vs. small chunk - strike a balance between semantic completeness and context length)
  * Metadata Attachments (page numbers, file names and even artificially constructed metadata like summary)
  * Structural Index
    * Hierarchical index structure
    * Knowledge Graph index
* Query optimization
  * Query Expansion
    * Multi-Query
    * Sub-Query
    * Chain-of-Verification(CoVe)
  * Query Transformation  
    * Query rewrite
    * LLM-modified query (use prompt engineering to let LLM generate a query based on the original query for subsequent retrieval)
  * Query Routing
    * Metadata Router/ Filter
    * Semantic Router
* Embedding
  * Mix/hybrid Retrieval
  * Fine-tuning Embedding Model
* Adapter
  * designed to accommodate multiple downstream tasks or to enhance performance on specific tasks

## The G of RAG - Generation

* Context Curation - further process the retrieved content
  * Reranking (reorders document chunks to highlight the most pertinent results first)
  * Context Selection/Compression 
* LLM Fine-tuning / RLHF

## The A of RAG - Augmentation

The standard practice often involves a singular (once) retrieval step followed by generation, which can lead to inefficiencies and sometimes is typically insufficient for complex problems demanding multi-step reasoning. So...

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240423-5.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    three types of retrieval augmentation processes
  </div>
</div>

* Iterative Retrieval
  * Iterative retrieval is a process where the knowledge base is repeatedly searched based on the initial query and the text generated so far.
* Recursive Retrieval
  * iteratively refining search queries based on the results obtained from previous searches
  * aiming to enhance the search experience by gradu- ally converging on the most pertinent information through a feedback loop
  * To address specific data scenarios, recursive retrieval and multi-hop retrieval techniques are utilized together
* Adaptive Retrieval
  * enabling LLMs to actively determine the optimal moments and content for retrieval

## Tasks & Evaluation

* Downstream tasks
  * QA
  * Infor- mation Extraction (IE)
  * dialogue generation
  * code search
* Evaluation Target
  * Historically: evaluated by downstream tasks criteria
  * Now: evaluate the distinct characteristics of RAG models
    * Retrieval Quality
    * Generation Quality
* Evaluation Aspects
  * Quality Scores
    * Context Relevance -> Retrieval Quality
    * Answer Faithfulness
    * Answer Relevance
  * Required Abilities
    * Noise Robustness -> Retrieval Quality
    * Negative Rejection
    * Information Integration
    * Counterfactual Robustness
* Evaluation Benchmarks and Tools

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240423-6.png" class="img-fluid rounded z-depth-1" zoomable=true%}
</div>


## Future Prospects

1. RAG vs Long Context. RAG still plays an irreplaceable role because a large amount of context at once will significantly impact its inference speed, and RAG-based generation can quickly locate the original references for LLMs to help users verify the generated an- swers, which is observable. Developing new RAG methods in the context of super-long contexts is one of the future research trends.
2. RAG Robustness. “Misinformation can be worse than no information at all”.
3. Hybrid Approaches = RAG + FT
4. Scaling laws of RAG. While scaling laws are established for LLMs, their applicability to RAG remains uncertain.
5. Production-Ready RAG. Customization, simplification, specialization.
6. Multi-modal RAG. (image, audio, video, code)

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240423-7.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    Summary of RAG Ecosystem
  </div>
</div>

## Personal Comment

Well I cannot give formal judgement to the survey quality because I am still a novice to RAG. My comment to the paper from the perspective of written language is that some sentences are too complicated, and too many repetitive expressions are used. However, flaws do not cover up strengths. The logic of the survey is clear enough for beginners. The illustrations are superb in my opinion, with forms of neat and pleasing simplicity and consice harmonic colors.