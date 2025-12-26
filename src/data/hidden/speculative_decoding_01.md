---
title: On Speculative Decoding and how to speed up Fine Tuned LLMs - 1
author: Hassan Sarwat
pubDatetime: 2025-06-01T10:00:00Z
slug: spec_decode_1
featured: true
draft: false
tags:
  - LLMs
  - Technical
  - Speculative Decoding
  - Chain of Draft
description:
  "The first part of a series explaining speculative decoding and my experiment on applying a new logic called chain of draft"
---

## Introduction

#### Why I started this (You can skip this section if you want)

I have recently finished my master's degree at TUM, and seeing that I'm unemployed (at the time of writing this blog, if this is not crossed out or deleted feel free to send me opportunities) and with more free time on my hand I've decided to explore and enhance my knowledge, and also get back my love for research after the mess that was my thesis.

Recently I was reading up on fine tuning model and their deployments, and I realized that while I do know a decent amount about fine-tuning LLMs and the different shortcuts you can take, from few shot prompting to parameter efficient fine tuning methods such as quantization or low rank adapters, I don't really know a lot about inference. 

I was working on a chatbot with friends and when we considered deploying our own fine-tuned models I became accutely aware of how expensive just maintaining it would be, not just that, when building a chatbot something like latency is very crucial to user experience. Given the tens of thousands of AI applications out there, any response we made had to be blazing fast but also shouldn't burn through our funds like a fire in forest.

My thought process was as follows, *if people speed up and improve LLM training, then surely something exists for LLM inference.* And I was correct. Having spent the last 3 years mostly working on pure research during my master's, inference speed was not a metric I cared for a lot. Very rarely was it mentioned in most papers and if it was it was written in the appendix, people usually cared more about being correct than being fast.

So I searched, and checked what methods are there for me to employ and how can I speed up inference, this is where I found speculative decoding[^1], and the further improvements such as Medusa[^2] and EAGLE[^3], with EAGLE-3[^4] currently being SOTA for inference improvements. 

Nevertheless I decided to go with speculative decoding, mainly due to the model agnoticism and freedom provided versus the being limited to a selection of models if I decided to go with the other methods, maybe later in the series I'll talk about them and their implementation but for now we start with Speculate Decoding. So

#### What is Speculative Decoding?

And how can it make my inference faster? Speculative decoding and other methods follow a very similar way of thinking. Why have the expensive big boss do the work when you can train a cheaper and fastern intern to act like it, and then the big boss just needs to approve needs to approve the work.

The way standard speculative decoding does it is by using a smaller draft model, in this case we fine-tune a Qwen2.5-14b-Instruct as our target model (big brain) and use a smaller Qwen2.5-0.5B-Instruct model as our draft model (small brain). Medusa on the hand works by training multiple heads and attaching them to the same model. So if we used Medusa our Qwen2.5-14b-Instruct would have the main head to predict token N, and additional *k* heads that will be used to predict tokens N+1, N+2, ..., N+k (normally k is 3). Where standard speculative decoding is trained on the output of the target model, EAGLE is a lightweight transformer that is trained on the second to top layer embeddings of the target model before the output, learning semantic context that wouldn't be possible otherwise. 

Here's a figure from the original EAGLE[^3] paper showing the comparisons 

![Comparisons](C:/Users/the25th/Documents/Hassan-Sarwat.github.io/src/assets/images/eagle_paper_image_comparisons.png)



If you are interested in reading more and maths is your thing, I've attached the references at the bottom, I won't say that I won't do a deep dive into the maths of it in a later part in the series but probably not this part.

### Problem Statement

For this project I wanted to apply speculative decoding, and also experiment a bit. My hypothesis is as follows, can we train a reasoning model on a summarized chain of thought, also called chain of draft, how does that affect metrics our metrics in terms of total tokens used per response and accuracy, does it fail at tasks that require extensive reasoning? So we make two hypothesis

1. Speculative decoding provides speedup over traditional LLM token prediction (we know this is true, but now we will implement it)
2. We can maintain the same reasoning performance and reduce token output by training on chain of draft instead of chain of thought.

The first problem is self explanatory, the second problems needs a bit more clarification. How do we define chain of draft, a summary is not exact enough for our very scientific brains. In this experiment our definition will be as follows. **A chain of draft must be 3-5 steps where each step has a maximum of 5 words.**

Why did we pick this intuition, what is the reason behind it? First, chain of thought includes a lot of rambling, more rambling means more tokens and more tokens mean slower speed. The second reason is because when doing speculative decoding, we want the target model to accept the tokens of the draft model, and the longer the sequences of tokens the higher chance of divergence and therefore more rejections from the main model.

![Comparisons](C:/Users/the25th/Documents/Hassan-Sarwat.github.io/src/assets/images/why-waste-time-when-few-word-do-trick.gif)

Those are our two problem statements. For now, we will purely focus on the implementation of speculative decoding, the first problem. In the later parts of the series we will do the tests and analysis for chain of draft, it's improvement over chain of thought, how much tokens were saved, was the accuracy different, how much faster is it? etc...

So now that we have our problem, what are we going to do. Our pipeline is basically as follows

1. Dataset generation 
2. Target Model Training 
3. Draft Model Training
4. Inference
5. Evaluation

### Dataset Generation

The fun step. So what dataset 




## References
[^1]: [Fast Inference from Transformers via Speculative Decoding](https://arxiv.org/pdf/2211.17192)
[^2]: [Medusa: Simple LLM Inference Acceleration Framework with Multiple Decoding Heads](https://arxiv.org/pdf/2401.10774)
[^3]:[EAGLE: Speculative Sampling Requires Rethinking Feature Uncertainty](https://arxiv.org/pdf/2401.15077)
[^4]:[EAGLE-3: Scaling up Inference Acceleration of Large Language
Models via Training-Time Test](https://arxiv.org/pdf/2503.01840)
[^5]:
[^6]:
[^7]:
[^8]:
[^9]:
[^10]: