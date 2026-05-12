---
layout: post
title: "Computer vision: a gentle introduction"
date: 2020-08-04
category: Engineering
summary: "An overview of core computer vision tasks — classification, detection, segmentation — and the convolutional building blocks behind them."
medium_url: "https://rugery-developper.medium.com/computer-vision-e7138d0c57d7"
---

*Originally published on [Medium](https://rugery-developper.medium.com/computer-vision-e7138d0c57d7).*

---

This is the first post in a series on computer vision. It covers the foundational concepts before diving into specific architectures in later posts.

## What is computer vision?

Computer vision is the field that teaches machines to interpret images and video. The main tasks are:

- **Classification + localization** — predict the class of a single object and draw a bounding box around it.
- **Object detection** — find and classify *multiple* objects in one image.
- **Semantic segmentation** — assign a class label to every pixel.
- **Instance segmentation** — segmentation applied per detected instance, distinguishing two dogs from each other rather than just labeling all dog pixels.

## How computers represent images

An image is a tensor of shape `(H, W, C)` where H and W are height and width in pixels, and C is the number of channels (3 for RGB). Each value is an integer from 0 to 255.

## The convolution operation

A convolutional layer slides a small filter (e.g. 3×3) across the input, computing a dot product at each position. This extracts local features — edges, corners, textures — while preserving spatial relationships. Multiple filters run in parallel, producing multiple feature maps.

## Pooling and upsampling

**Max pooling** reduces spatial resolution by taking the maximum value in each local region. This provides translation invariance and reduces computation. **Upsampling** (nearest-neighbor, bilinear, or transposed convolution) reconstructs spatial resolution — essential for segmentation heads.

## A practical example: MNIST

A small CNN trained on MNIST (handwritten digits, 28×28 pixels) with two conv layers, max pooling, dropout, and a softmax head achieves **99.03% accuracy** — illustrating how much representational power even a shallow convolutional network has.

---

[**Read the full article on Medium →**](https://rugery-developper.medium.com/computer-vision-e7138d0c57d7)
