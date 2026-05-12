---
layout: post
title: "RAFT: Recurrent All-Pairs Field Transforms for optical flow"
date: 2020-10-20
category: Paper reading
summary: "How RAFT's correlation pyramid and GRU-based iterative updates set a new bar for optical flow estimation — ECCV 2020 Best Paper."
medium_url: "https://becominghuman.ai/recurrent-all-pairs-field-transforms-for-optical-flow-98cf4dc05cc4"
---

*Originally published in [Becoming Human: Artificial Intelligence Magazine](https://becominghuman.ai/recurrent-all-pairs-field-transforms-for-optical-flow-98cf4dc05cc4).*

---

**Optical flow** is the task of estimating the apparent motion of each pixel between two consecutive frames. It's a core component of video understanding and autonomous driving pipelines. RAFT (Teed & Deng, ECCV 2020 Best Paper) reduced the F1-all error by **16%** over the previous state of the art.

## The three components

### 1. Feature encoder

Two convolutional networks process the pair of input frames. One extracts per-pixel features for correlation; the other extracts context features used to initialize and update the GRU's hidden state.

### 2. All-pairs correlation volume

Rather than computing flow at a single scale, RAFT builds a **4D correlation volume** — every pixel in frame 1 is compared against every pixel in frame 2 via dot product. This volume is then pooled at multiple scales, creating a correlation pyramid. During iterative refinement, the network can look up correlation values at any candidate flow offset, giving it access to both fine-grained and coarse matching signals.

### 3. Iterative GRU updates

RAFT uses a convolutional GRU to iteratively refine a flow estimate starting from zero. At each step, it:
1. Samples the correlation pyramid at the current flow estimate's offset.
2. Concatenates correlation features with context features and the current flow.
3. Passes through the GRU to produce a flow update (delta).

Iterating 12–32 times yields progressively sharper estimates. During training, intermediate estimates are supervised with exponentially increasing weights — encouraging the network to converge quickly.

## Why it generalizes well

Previous methods often required per-dataset fine-tuning. RAFT's separation of correlation (data-driven matching) from the GRU update (learned refinement) makes the representation more transferable. Trained only on synthetic data (FlyingChairs + FlyingThings), it generalizes well to Sintel and KITTI with minimal fine-tuning.

## Key result

On the KITTI-15 benchmark, RAFT achieves **5.10% F1-all** — a 16% reduction in error over the prior best. It also runs at ~20 FPS on a 1080Ti, making it viable for real-time applications.

---

[**Read the full article on Medium →**](https://becominghuman.ai/recurrent-all-pairs-field-transforms-for-optical-flow-98cf4dc05cc4)
