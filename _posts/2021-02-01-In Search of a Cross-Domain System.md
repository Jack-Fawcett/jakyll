---
tags: DDTCDR ETL Recommendation System Dual Transfer Cross Domain Equivalent Transformation User Preferences
categories: Recommendation
excerpt: Two papers I bumped into when I was looking for a recommendation which supported users in multiple similar yet different systems. DDTCDR Deep Dual Transfer Cross Domain Recommendation and Towards Equivalent Transformation of User Preferences in Cross Domain Recommendation.
header:
  teaser: "/assets/images/dual.png"
---

Recently I was looking for a recommendation which supported users in multiple similar yet different systems. Below are two short summaries of interesting papers I came across.

## DDTCDR: Deep Dual Transfer Cross Domain Recommendation

[Paper Link](https://arxiv.org/pdf/1910.05189v1.pdf)

The idea behind the paper is to allow two recommendation systems to communicate in training using a shared orthogonal matrix. Here they are using a simple shallow neural network (MLP).

![In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled.png](/jakyll/assets/In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled.png)

The author of the paper recommended to embed the user and item features pre-training the recommender for a smoother less complex training process. According to the paper, the type of auto-encoder made no difference to the model.

Interestingly each dataset is passed through each model and then the final rating prediction is calculated using a combination of the two using a hyperparameter alpha. For their experiment alpha is 0.03 which you can think of as allowing the other network to influence the prediction by 3%.

![In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%201.png](/jakyll/assets/In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%201.png)

Due to the architecture of the system, it would be difficult to use factorization machines in place of the standard deep neural network. This means that we lose some of the useful collaborative filtering benefits that come with it.

At least in my experimentation I could not get the model to achieve better results than two separate matrix factorisation machines.

## Towards Equivalent Transformation of User Preferences in Cross Domain Recommendation

[This paper](https://dl.acm.org/doi/abs/10.1145/3357384.3357992) uses similar ideas to that of DDTCDR but uses two adversarial auto-encoders in place of a separate encoder with an MLP layer. The key being trying to model a join distribution of the users in both domains.

![In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%202.png](/jakyll/assets/In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%202.png)

While trying to capture both domain specific features, as well as common ones.

![In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%203.png](/jakyll/assets/In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%203.png)

Several different distributions were tested as the prior but Gaussian worked out to be the best.

The key assumption of the paper is that of Equivalent Transform. Given the observations in each domain, the latent variables are conditionally independent, as so:

![In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%204.png](/jakyll/assets/In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%204.png)

This can be interpreted as, given just an observation from one domain, I can recreate (encode) to the latent space, combining with the other latent variables I can reach my joint distribution.

![In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%205.png](/jakyll/assets/In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%205.png)

The algorithm has two loss terms:

![In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%206.png](/jakyll/assets/In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%206.png)

The first is the joint reconstruction loss which can be seen as the linked loss of encoding, decoding and regularization for observations. How well do I reconstruct my observations given both domains.

The second is the prior regularization loss which enforces that the latent variables are of the same space as the prior (Gaussian). This is achieved using an adversarial discriminator rather than trying to model the KL divergence for all terms.

The model requires that users have ratings in both domains which can limit the application to users who have a ‘cold start' in one domain.

Through their benchmarking the model performs better than many other cross-domain algorithms (including DDTCDR)

![In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%207.png](/jakyll/assets/In%20Search%20of%20a%20Cross-Domain%20System%20aa147349817642ffb0fd9fcab9b18f32/Untitled%207.png)

HR: Hit rate, NDCG: Normalised Discounted Cumulative Gain, MRR: Mean reciprocal Rank. All very common metrics for Click through rate recommender systems.

On top of filtering out users who do not appear in both domains, the authors suggest filtering users with less than 5 observations in a domain, and items with less than 5 user ratings.