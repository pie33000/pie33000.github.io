---
layout: post
title: "Attention is all you need"
date: 2020-07-21
category: Paper reading
summary: "An explanation of the transformer architecture and why self-attention outperforms recurrent networks for sequence tasks."
medium_url: "https://becominghuman.ai/attention-is-all-you-need-16bf481d8b5c"
---

*Originally published in [Becoming Human: Artificial Intelligence Magazine](https://becominghuman.ai/attention-is-all-you-need-16bf481d8b5c).*

---

The 2017 paper "Attention Is All You Need" (Vaswani et al.) introduced the **transformer**, which has since become the dominant architecture across NLP and, increasingly, computer vision and robotics. This post walks through the core ideas.

## Why not RNNs?

Recurrent networks like LSTMs process sequences one token at a time, passing a hidden state forward. This sequential dependency makes them hard to parallelize and causes gradients to vanish over long sequences. The transformer removes recurrence entirely — every position in the sequence attends to every other position simultaneously.

## The encoder-decoder structure

The original transformer is designed for sequence-to-sequence tasks (e.g. machine translation). It has two halves:

- **Encoder**: reads the input sequence and produces a set of continuous representations.
- **Decoder**: generates the output sequence one token at a time, attending to the encoder's output at each step.

Both halves are stacks of identical layers, each containing a multi-head self-attention block and a position-wise feed-forward network.

## Multi-head attention

The attention mechanism computes, for each position, a weighted sum of all other positions' values — where weights are determined by how "relevant" each position is. Formally:

```
Attention(Q, K, V) = softmax(QKᵀ / √d_k) · V
```

*Multi-head* attention runs this operation in parallel with different learned projections, then concatenates the results. This lets the model jointly attend to information from different representation subspaces.

## Positional encoding

Because there is no recurrence, the model has no inherent sense of order. Positional encodings — sinusoidal functions added to the input embeddings — inject sequence position information.

## Why it matters

Transformers parallelize trivially during training, scale well with data and compute, and transfer across tasks via pretraining. They are the foundation of GPT, BERT, ViT, and most modern large models.

---

[**Read the full article on Medium →**](https://becominghuman.ai/attention-is-all-you-need-16bf481d8b5c)
