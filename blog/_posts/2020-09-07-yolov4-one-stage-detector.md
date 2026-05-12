---
layout: post
title: "Explaining YOLOv4: a one-stage detector"
date: 2020-09-07
category: Paper reading
summary: "A deep dive into the YOLOv4 architecture — backbone, neck, head, and the bag-of-tricks that make it fast enough for autonomous vehicles."
medium_url: "https://becominghuman.ai/explaining-yolov4-a-one-stage-detector-cdac0826cbd7"
---

*Originally published in [Becoming Human: Artificial Intelligence Magazine](https://becominghuman.ai/explaining-yolov4-a-one-stage-detector-cdac0826cbd7).*

---

Object detection splits into two camps: **two-stage detectors** (propose regions, then classify — accurate but slow) and **one-stage detectors** (predict boxes and classes in a single pass — fast enough for real-time). YOLO ("You Only Look Once") is the canonical one-stage family. YOLOv4 (Bochkovskiy et al., 2020) assembles a careful selection of recent tricks into a detector that runs in real time on a single GPU.

## Architecture overview

YOLOv4 follows the standard detector structure: **backbone → neck → head**.

### Backbone: CSPDarknet53

The backbone extracts features from the input image. CSPDarknet53 builds on Darknet53 with **Cross Stage Partial (CSP)** connections — splitting the feature map at each stage and merging via concatenation. This reduces computation while maintaining accuracy.

Key training tricks applied to the backbone:
- **Mish activation** (`x · tanh(softplus(x))`) instead of Leaky ReLU — smoother gradient flow.
- **Mosaic augmentation** — four images combined into one during training, forcing the model to detect small objects in varied contexts.
- **DropBlock regularization** — structured dropout that zeroes out contiguous regions rather than random pixels.

### Neck: SPP + PANet

The neck aggregates features at multiple scales, which is critical for detecting objects of different sizes.

- **Spatial Pyramid Pooling (SPP)** applies max pooling with several kernel sizes in parallel and concatenates the results, significantly expanding the receptive field.
- **Path Aggregation Network (PANet)** adds a bottom-up path to the standard FPN top-down path, shortening the information flow between low-level and high-level features.

### Head: YOLOv3 head with CIoU loss

The detection head is unchanged from YOLOv3 — three scales, each predicting bounding boxes via anchor offsets plus objectness and class scores.

The main upgrade is the loss function: **CIoU** (Complete IoU) replaces MSE for box regression. It accounts for overlap area, center distance, *and* aspect ratio difference — providing a more meaningful gradient when boxes don't overlap at all.

## Why it matters for autonomous vehicles

The combination of real-time speed (65 FPS on a V100 at 608×608) and strong accuracy (43.5% AP on COCO) makes YOLOv4 a practical choice for embedded deployment in autonomous driving pipelines, where latency directly affects safety margins.

---

[**Read the full article on Medium →**](https://becominghuman.ai/explaining-yolov4-a-one-stage-detector-cdac0826cbd7)
