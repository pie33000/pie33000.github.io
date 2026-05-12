---
layout: post
title: "SEER: Self-supervised pretraining of visual features in the wild"
date: 2021-03-29
category: Paper reading
summary: "How Meta's SEER combines SwAV clustering and the RegNetY architecture to pretrain billion-parameter vision models on uncurated Instagram images."
medium_url: "https://becominghuman.ai/seer-self-supervised-pretraining-of-visual-features-in-the-wild-c1a3387ad9ec"
---

*Originally published in [Becoming Human: Artificial Intelligence Magazine](https://becominghuman.ai/seer-self-supervised-pretraining-of-visual-features-in-the-wild-c1a3387ad9ec).*

---

Most self-supervised vision models are pretrained on ImageNet — a curated, balanced, label-free version of which is used for contrastive methods. SEER (Goyal et al., Meta AI, 2021) asks: can we pretrain directly on **random, uncurated internet images** at scale? The answer is yes, and it reaches supervised ImageNet performance without any labels.

## The three building blocks

### SwAV: clustering-based self-supervision

Traditional contrastive methods (SimCLR, MoCo) learn by pushing embeddings of augmented views of the same image together and other images apart. SwAV replaces negative pairs with **online clustering**: it maintains a set of learnable prototypes and trains the network to predict, from one augmented view, the cluster assignments of another view. This avoids the memory cost of large negative queues and works well with smaller batch sizes.

The key formula is a **swapped prediction** loss:
```
L = -log p(z_t | q_s) - log p(z_s | q_t)
```
where `z` are feature vectors and `q` are cluster assignments (computed via the Sinkhorn-Knopp algorithm to enforce equipartition across clusters).

### RegNetY: scalable network design

SEER uses RegNetY as its backbone — a family of networks discovered by designing a *design space* (parameterized by width, depth, group convolutions) and sampling it via random search under a compute budget. RegNetY adds Squeeze-and-Excitation blocks to RegNet, improving accuracy at equal FLOP count. The design space philosophy makes it easy to scale: SEER was trained with models up to **1 billion parameters**.

### Uncurated pretraining data

Rather than ImageNet, SEER trains on a random sample of **1 billion public Instagram images** — no curation, no balancing. This tests whether self-supervised objectives can extract useful structure from data with the noise distribution of the real web.

## Results

A SEER RegNetY-256GF pretrained on 1B Instagram images and fine-tuned on ImageNet with 10% of labels reaches **77.9% top-1** — matching supervised baselines that use 100% of labels. On transfer tasks (iNaturalist, Places365), it outperforms supervised ImageNet pretraining, suggesting that diversity in pretraining data matters more than curation.

## Why it matters

SEER demonstrated that the self-supervised pretraining paradigm scales beyond curated datasets, pointing toward a future where large vision models are pretrained on raw web data — the visual analogue of how language models are trained.

---

[**Read the full article on Medium →**](https://becominghuman.ai/seer-self-supervised-pretraining-of-visual-features-in-the-wild-c1a3387ad9ec)
