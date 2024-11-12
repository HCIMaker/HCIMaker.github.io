---
layout: post
title: Attention Is All You Need Explained By Non-CS Major
date: 2024-11-11
description: 
tags: Machine-Learning
categories: IIya 30u30
giscus_comments: true
related_posts: false
toc:
  sidebar: left
---
<a href='https://arxiv.org/pdf/1706.03762'>Original Paper</a>

## Modified Heilmeier Catechism
1. What does the paper try to do?  
The paper proposes a novel deep learning model structure for sequence-to-sequence tasks, largely improving the computation efficieny (reduction of training time) 
of the deep learning model. The paper also inspires the ubiquitious usage of the novel model structure to the generation task.

2. What are the limits of the practice at that time?  
The computation times of relating two arbitrary input or output positions grows in the distance between positions.(linearly or logarithmically)

3. What is novel in this paper? And why it is successful?  
The novel deep learning model structure are fully based on the attention mechanism relating tokens at different positions of a single(multiple) sequence(s).
This attention mechanism allows a more significant parallelization during the training stage, and it achieves a better performance at the same time.

4. What is the impact?  
The attention structure becomes the foundation of the current revolutionized AI-Generated Content (AIGC) technology trend due to its striking improvement on the computation efficacy.

## Model Structure
[Source Website: The Annotated Transformer](https://nlp.seas.harvard.edu/annotated-transformer/)
to-be-continued
