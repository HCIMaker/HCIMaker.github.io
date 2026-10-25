---
layout: post
title: Do We Still Need to Train Customized Models?
date: 2026-10-14
description: 
tags: [Embedded-System]
categories:
giscus_comments: true
related_posts: false
toc:
  sidebar: left
published: false
---

Today's most capable AI systems are trained by big companies in massive data centers. Unfortunately, academic labs rarely have that much compute or data, let alone enough to run many experiments in parallel. That is even more true for a Ph.D. student like me, who isn't working on frontier models at all.

However, the recent wave of open-source foundation models raises a question for me: do we still need to train a new model for every downstream task? Take DINOv3 from Meta. It is trained without supervision on **X** images with **Y** parameters, and its features are so good that you can see its understanding of an image just by looking at the attention maps. Similarly, TimesFM from Google is pretrained on **X_1** time points with **Y_1** parameters to forecast future values of a time series.

Even fine-tuning these models is hard without a decent GPU budget. So instead, what if we freeze them and rely on classical probability theory? The idea has two parts:

1. **Use the foundation model as a fixed feature extractor.** Its embeddings already capture the most important, general-purpose information in the raw data, so we only need a lightweight classifier on top.
2. **Adapt the outputs, not the model.** When our real-world data differ from the training data (for example, the class frequencies change), we correct the classifier's output probabilities instead of retraining it. This idea goes back at least to Saerens et al. (2002), who proposed "a procedure allowing the correction of the estimated a posteriori probabilities, that is, the classifier's outputs, in accordance with the new a priori probabilities of the real-world data, in order to make more accurate predictions, even if these a priori probabilities of the new data set are not known in advance."

Concretely, if the classifier was trained with class priors $$p(y)$$ and the new data have priors $$p_{\text{new}}(y)$$, the corrected posterior is simply

$$
p_{\text{new}}(y \mid x) \propto p(y \mid x) \, \frac{p_{\text{new}}(y)}{p(y)}
$$

In other words, it is proportional to the increasing of prior probabilitiy.

This is based on the assumption that the likelihood (The original paper called it the density of within-class probabilities remains the same)

$$
p (x \mid y) = p_{\text{new}}(x \mid y)
$$


