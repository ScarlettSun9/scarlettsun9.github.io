---
layout: distill
title: Paper Review 📝 & Play 🤹‍♀️ - Two Papers on the Application of LangChain
description: One is about automating customer service, the other is in mathematical problem solving using LLMMathChain.
tags: LangChain
giscus_comments: true
date: 2024-04-18
featured: false


# Optionally, you can add a table of contents to your post.
# NOTES:
#   - make sure that TOC names match the actual section names
#     for hyperlinks within the post to work correctly.
#   - we may want to automate TOC generation in the future using
#     jekyll-toc plugin (https://github.com/toshimaru/jekyll-toc).
toc:
  - name: First Paper
  - name: Second Paper
    # if a section has subsections, you can add them as follows:
    # subsections:
    #   - name: Example Child Subsection 1
    #   - name: Example Child Subsection 2

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

## First paper

### [Automating Customer Service using LangChain: Building custom open-source GPT Chatbot for organizations](https://arxiv.org/abs/2310.05421)

This paper talks about the procedure to use LangChain and LLM to build a customer-service chatbot and take the website of an education institution as an example. First the author use BeautifulSoup to scrape most of the data from the website as the dataset for the experiment. Then they choose Huggingface Instruct Embeddings as the text embedding, because it can generate tailored embeddings without further finetuning. Then they choose Google's Flan T5 XXL as the language model an deploy the whole process using Gradio API. 

<div>
  {% include figure.liquid loading="eager" path="assets/img/20240418-1.png" class="img-fluid rounded z-depth-1" zoomable=true%}
  <div class="caption">
    Model Architecture
  </div>
</div>

The steps of the service are: The embeddings of the dataset are sent to the model ➡️ The user input the question ➡️ get question embeddings and sent to the model ➡️ the model generate the answer ➡️ continue...

**Several points worth noting:**

1. The function of the datset. QA pairs are the best data for customer service llm, but it is only a small portion of the website. For other content of the website, it might not enough for the model to learn the domain knowledge. In other words, the data on a single website might not be enough for finetuning in my opinion;
2. The authors also mentioned they used "LangChain" for finetuning, but this step is not in the paper;
3. The comparison of models in this experiment is not useful. It would be better to compare different LLMs such as the Flan T5 here and LLaMA etc, instead of compare the effect of model with different size in the same series.
3. Huggingface Instruct Embeddings - should give it a try.


## Second Paper

### [An Analysis of Large Language Models and LangChain in Mathematics Education](https://www.researchgate.net/publication/374590545_An_Analysis_of_Large_Language_Models_and_LangChain_in_Mathematics_Education)

This paper is more about qualitative analysis on the mathematical problem solving ability of ChatGPT and LLMMathChain. Two types of math problems (word problem and numerical computational problem) and five error types are proposed; seven problems and the generated answeres are analyzed. The result is, in this experiment, ChatGPT's accuracy on math solving is higher than LLMMathChain, which has generated answers containing four out of five types of errors. The authors have pointed out the essential difference between math problem solving and LLM generation, the former is deterministic and deductive, while the latter is probablistic and inductive. To achieve better result for LLM on math, more data are needed for the model to learn the deductive reasoning process. Besides, LLM + LangChain + Chain of Thought (CoT) + decision tree + etc. can be helpful for future mathematic models.

**Comments:**

The content here is more about qualitative analysis instead of algorithm or experimental discussion. So there aren't too much to say. One thing needs to point out is the error types and examples. The authors only provides seven examples in the paper and then there are five error types, which seem not convincing or generalizable. It would be better to test more math examples and summarize the common mistakes models made. 

