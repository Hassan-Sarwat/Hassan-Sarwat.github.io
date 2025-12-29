---
title: On Chain of Draft Speculative Decoding and how to speed up your LLMs - 1
author: Hassan Sarwat
pubDatetime: 2025-12-28T20:12:32Z
slug: spec_decode_0
featured: true
draft: false
tags:
  - LLMs
  - Technical
  - Speculative Decoding
  - Chain of Draft
description:
  "The second part of the series explaining speculative decoding and my plan on applying a chain of draft instead of chain of thought when training reasoning models. This part dives into dataset generation and selection"
---

# Dataset Generation and Evaluation

This the second part of the series on Chain of Draft Speculative Decoding. In this part we will talk about which dataset to pick and how to generate them.

We want to apply the logic from the paper from Magister et al[^1], where they teach small models to reason. For that we will fine-tune them on Chain of Thought Data and Chain of Draft, and compare the results. 

Let's have a quick reminder of our hypotheses:
* Fine-tuning the model on reasoning will improve its accuracy
* Speculative decoding will reduce our inference time without affecting accuracy
* Chain of draft will reduce our total tokens with only minimal effect on accuracy

Looking at the requirements we notice that we will need to stress test our ideas under 3 conditions, an easy condition where our untrained 14B model can solve, and we mainly observe the impact on inference speed and accuracy when using speculative decoding and reasoning, this should be the case where you ask yourself "should I train the model with reasoning or not". 

The second test will be a medium difficulty setting where our untrained target model can solve some questions, but fine-tuning will notably improve the accuracy. In this one we note the speedup using speculative decoding and also compare the accuracy of chain of draft vs chain of thought, can our small draft models keep up where even our target models have difficulty.

The final case will be the robustness test, a dataset that our untrained target model can't solve and only when it reasons can it begin to solve, we want to see can chain of draft keep up with chain of thought under difficult conditions, and how does it impact our acceptance rate.

Based on these conditions we decide on the following datasets

1. GSM8K[^2]: This is our easy dataset, it consists of grade school math questions and even our untrained model 
2. MATH[^3]: Our medium difficulty dataset, 
3. GSM-Hard[^4]:
## References
[^1]:[Teaching Small Language Models to Reason](https://aclanthology.org/2023.acl-short.151.pdf)
[^2]:[Training Verifiers to Solve Math Word Problems](https://arxiv.org/abs/2110.14168)
[^3]:[Measuring Mathematical Problem Solving with MATH Dataset](https://arxiv.org/pdf/2103.03874)
[^4]:[PAL: Program-aided Language Models](https://arxiv.org/pdf/2211.10435)