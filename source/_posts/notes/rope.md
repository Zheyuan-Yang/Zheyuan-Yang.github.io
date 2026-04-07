---
title: RoPE
date: 2026-03-22
categories: notes
mathjax:
  tags: none # or 'ams' or 'all'
  single_dollars: true # enable single dollar signs as in-line math delimiters
  cjk_width: 0.9 # relative CJK char width
  normal_width: 0.6 # relative normal (monospace) width
  append_css: true # add CSS to pages rendered by MathJax
  every_page: false # if true, every page will be rendered by MathJax regardless the `mathjax` setting in Front-matter
  packages: # extra packages to load
  extension_options: {}
---

## Abstract

This document provides a rigorous mathematical treatment of the correspondence between token positions and rotation matrices in Rotary Position Embedding (RoPE). We define the mapping $m \mapsto \mathbf{R}_m$ and establish its key properties that enable relative position encoding in transformer attention mechanisms.

---

## 1. Introduction

Rotary Position Embedding (RoPE) encodes positional information by applying position-dependent rotation transformations to query and key vectors. The core of RoPE is the mapping from integer positions to rotation matrices.

---

## 2. Basic Definitions

**Definition 1 (2D Rotation Matrix).** For a token at position $m \in \mathbb{N}$ and rotation angle parameter $\theta \in \mathbb{R}^+$, the basic 2D rotation matrix is defined as:

$$\mathbf{R}_m(\theta) = \begin{pmatrix} \cos(m\theta) & -\sin(m\theta) \\ \sin(m\theta) & \cos(m\theta) \end{pmatrix}$$

This matrix rotates a 2D vector counterclockwise by angle $m\theta$ radians.

---

## 3. Frequency Schedule

**Definition 2 (Frequency Schedule).** For a model with attention dimension $d$ (where $d$ is even), we define a sequence of frequencies for dimension pairs $i \in {0, 1, \ldots, d/2-1}$:

$$\theta_i = \beta^{-2i/d}$$

where $\beta$ is the base frequency, typically $\beta = 10000$.

**Interpretation:**

- Low index pairs ($i \approx 0$): $\theta_i \approx 1$ (high frequency, short wavelength)
- High index pairs ($i \approx d/2-1$): $\theta_i \approx 10000^{-1}$ (low frequency, long wavelength)

This creates a spectrum of wavelengths: $\lambda_i = 2\pi/\theta_i = 2\pi \cdot 10000^{2i/d}$, ranging from $2\pi$ to $2\pi \cdot 10000$.

---

## 4. High-Dimensional Rotation Matrix

**Definition 3 (RoPE Rotation Matrix).** For a $d$-dimensional space (with $d$ even) and position $m \in \mathbb{N}$, the RoPE rotation matrix $\mathbf{R}_m \in \mathbb{R}^{d \times d}$ is defined as a block-diagonal matrix:

$$\mathbf{R}_m = \begin{pmatrix} \mathbf{R}_m^{(0)} & \mathbf{0} & \cdots & \mathbf{0} \\ \mathbf{0} & \mathbf{R}_m^{(1)} & \cdots & \mathbf{0} \\ \vdots & \vdots & \ddots & \vdots \\ \mathbf{0} & \mathbf{0} & \cdots & \mathbf{R}_m^{(d/2-1)} \end{pmatrix}$$

where each $2 \times 2$ block is:

$$\mathbf{R}_m^{(i)} = \begin{pmatrix} \cos(m\theta_i) & -\sin(m\theta_i) \\ \sin(m\theta_i) & \cos(m\theta_i) \end{pmatrix}$$

---

## 5. Explicit Form

For dimension $d$, the full matrix $\mathbf{R}_m$ can be written explicitly as:

$$\mathbf{R}_m = \begin{pmatrix} \cos(m\theta_0) & -\sin(m\theta_0) & 0 & 0 & \cdots & 0 & 0 \\ \sin(m\theta_0) & \cos(m\theta_0) & 0 & 0 & \cdots & 0 & 0 \\ 0 & 0 & \cos(m\theta_1) & -\sin(m\theta_1) & \cdots & 0 & 0 \\ 0 & 0 & \sin(m\theta_1) & \cos(m\theta_1) & \cdots & 0 & 0 \\ \vdots & \vdots & \vdots & \vdots & \ddots & \vdots & \vdots \\ 0 & 0 & 0 & 0 & \cdots & \cos(m\theta_{d/2-1}) & -\sin(m\theta_{d/2-1}) \\ 0 & 0 & 0 & 0 & \cdots & \sin(m\theta_{d/2-1}) & \cos(m\theta_{d/2-1}) \end{pmatrix}$$

---

## 6. Key Properties

**Property 1 (Orthogonality).** For all $m \in \mathbb{N}$, $\mathbf{R}_m$ is an orthogonal matrix:

$$\mathbf{R}_m^T \mathbf{R}_m = \mathbf{R}_m \mathbf{R}_m^T = \mathbf{I}_d$$

where $\mathbf{I}_d$ is the $d \times d$ identity matrix.

_Proof._ Each 2D block $\mathbf{R}_m^{(i)}$ is a rotation matrix, hence orthogonal. Since $\mathbf{R}_m$ is block-diagonal with orthogonal blocks, it is also orthogonal. $\square$

---

**Property 2 (Inverse/Transpose).** The inverse of $\mathbf{R}_m$ equals its transpose:

$$\mathbf{R}_m^{-1} = \mathbf{R}_m^T = \mathbf{R}_{-m}$$

---

**Property 3 (Group Structure).** The set ${\mathbf{R}_m : m \in \mathbb{Z}}$ forms a group under matrix multiplication:

$$\mathbf{R}_m \mathbf{R}_n = \mathbf{R}_{m+n}$$

for all $m, n \in \mathbb{Z}$.

_Proof._ For each block $i$:

$$\mathbf{R}_m^{(i)} \mathbf{R}_n^{(i)} = \begin{pmatrix} \cos(m\theta_i) & -\sin(m\theta_i) \\ \sin(m\theta_i) & \cos(m\theta_i) \end{pmatrix} \begin{pmatrix} \cos(n\theta_i) & -\sin(n\theta_i) \\ \sin(n\theta_i) & \cos(n\theta_i) \end{pmatrix} = \begin{pmatrix} \cos((m+n)\theta_i) & -\sin((m+n)\theta_i) \\ \sin((m+n)\theta_i) & \cos((m+n)\theta_i) \end{pmatrix} = \mathbf{R}_{m+n}^{(i)}$$

by the angle addition formulas for cosine and sine. $\square$

---

**Property 4 (Determinant).** For all $m \in \mathbb{N}$:

$$\det(\mathbf{R}_m) = 1$$

---

## 7. The Crucial Relative Position Property

**Proposition 1 (Relative Position Encoding).** For query vector $\mathbf{q}_m$ at position $m$ and key vector $\mathbf{k}_n$ at position $n$, the attention score with RoPE satisfies:

$$(\mathbf{R}_m \mathbf{q}_m)^T (\mathbf{R}_n \mathbf{k}_n) = \mathbf{q}_m^T \mathbf{R}_{n-m} \mathbf{k}_n$$

_Proof._ Using the orthogonality and group properties:

$$(\mathbf{R}_m \mathbf{q}_m)^T (\mathbf{R}_n \mathbf{k}_n) = \mathbf{q}_m^T \mathbf{R}_m^T \mathbf{R}_n \mathbf{k}_n = \mathbf{q}_m^T \mathbf{R}_{-m} \mathbf{R}_n \mathbf{k}_n = \mathbf{q}_m^T \mathbf{R}_{n-m} \mathbf{k}_n \quad \square$$

This shows that the attention score depends only on the **relative position** $n-m$, not the absolute positions $m$ and $n$ individually.

---

## 8. Concrete Examples

### Example 1: $d=2$ dimensions

For $d=2$, we have only one frequency: $\theta_0 = 1$.

At position $m=0$:

$$\mathbf{R}_0 = \begin{pmatrix} 1 & 0 \\ 0 & 1 \end{pmatrix} = \mathbf{I}_2$$

At position $m=1$:

$$\mathbf{R}_1 = \begin{pmatrix} \cos(1) & -\sin(1) \\ \sin(1) & \cos(1) \end{pmatrix} \approx \begin{pmatrix} 0.540 & -0.841 \\ 0.841 & 0.540 \end{pmatrix}$$

At position $m=5$:

$$\mathbf{R}_5 = \begin{pmatrix} \cos(5) & -\sin(5) \\ \sin(5) & \cos(5) \end{pmatrix} \approx \begin{pmatrix} 0.284 & 0.959 \\ -0.959 & 0.284 \end{pmatrix}$$

### Example 2: $d=4$ dimensions

For $d=4$, we have two frequencies:

$$\theta_0 = 10000^{0} = 1, \qquad \theta_1 = 10000^{-1/2} = 0.01$$

At position $m=3$:

$$\mathbf{R}_3 = \begin{pmatrix} \cos(3) & -\sin(3) & 0 & 0 \\ \sin(3) & \cos(3) & 0 & 0 \\ 0 & 0 & \cos(0.03) & -\sin(0.03) \\ 0 & 0 & \sin(0.03) & \cos(0.03) \end{pmatrix} \approx \begin{pmatrix} -0.990 & 0.141 & 0 & 0 \\ -0.141 & -0.990 & 0 & 0 \\ 0 & 0 & 0.9996 & -0.0300 \\ 0 & 0 & 0.0300 & 0.9996 \end{pmatrix}$$

Notice how the first dimension pair rotates much faster (larger angle) than the second pair.

---

## 9. Application to Vectors

When applying $\mathbf{R}_m$ to a vector $\mathbf{q} = [q_0, q_1, q_2, q_3, \ldots, q_{d-1}]^T$, the result is:

$$\mathbf{R}_m \mathbf{q} = \begin{bmatrix} q_0 \cos(m\theta_0) - q_1 \sin(m\theta_0) \\ q_0 \sin(m\theta_0) + q_1 \cos(m\theta_0) \\ q_2 \cos(m\theta_1) - q_3 \sin(m\theta_1) \\ q_2 \sin(m\theta_1) + q_3 \cos(m\theta_1) \\ \vdots \\ q_{d-2} \cos(m\theta_{d/2-1}) - q_{d-1} \sin(m\theta_{d/2-1}) \\ q_{d-2} \sin(m\theta_{d/2-1}) + q_{d-1} \cos(m\theta_{d/2-1}) \end{bmatrix}$$

Each consecutive pair $(q_{2i}, q_{2i+1})$ undergoes a 2D rotation by angle $m\theta_i$.

---

## 10. Computational Complexity

**Matrix construction:** $O(d)$ to compute the angles and construct the block-diagonal structure.

**Matrix-vector multiplication:** $O(d)$ since $\mathbf{R}_m$ is sparse (block-diagonal).

In practice, implementations avoid explicit matrix construction and directly compute the rotated coordinates element-wise.

---

## 11. Odd Dimensions: The $d=3$ Case

RoPE is designed for even dimensions where all components can be paired for 2D rotations. However, it can still be applied to odd-dimensional spaces.

### Handling Unpaired Dimensions

For odd $d$, the standard approach is to rotate the first $d-1$ dimensions (which form $\lfloor d/2 \rfloor$ pairs) and leave the last dimension unrotated.

**Definition 4 (RoPE for Odd Dimensions).** For odd dimension $d = 2k + 1$ where $k = \lfloor d/2 \rfloor$, the rotation matrix is:

$$\mathbf{R}_m = \begin{pmatrix} \mathbf{R}_m^{(0)} & & & & \\ & \mathbf{R}_m^{(1)} & & & \\ & & \ddots & & \\ & & & \mathbf{R}_m^{(k-1)} & \\ & & & & 1 \end{pmatrix}$$

where the last $1 \times 1$ block leaves dimension $d-1$ unrotated.

### Example: $d=3$

For $d=3$, we have $k=1$ pair and one unpaired dimension.

**Frequency:** $\theta_0 = 10000^{0/3} = 1$

**Rotation matrix structure:**

$$\mathbf{R}_m = \begin{pmatrix} \cos(m\theta_0) & -\sin(m\theta_0) & 0 \\ \sin(m\theta_0) & \cos(m\theta_0) & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

At position $m=0$:

$$\mathbf{R}_0 = \begin{pmatrix} 1 & 0 & 0 \\ 0 & 1 & 0 \\ 0 & 0 & 1 \end{pmatrix} = \mathbf{I}_3$$

At position $m=2$:

$$\mathbf{R}_2 = \begin{pmatrix} \cos(2) & -\sin(2) & 0 \\ \sin(2) & \cos(2) & 0 \\ 0 & 0 & 1 \end{pmatrix} \approx \begin{pmatrix} -0.416 & -0.909 & 0 \\ 0.909 & -0.416 & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

At position $m=\pi/2 \approx 1.571$:

$$\mathbf{R}_{\pi/2} = \begin{pmatrix} 0 & -1 & 0 \\ 1 & 0 & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

This represents a 90-degree rotation in the $(q_0, q_1)$ plane, while $q_2$ remains fixed.

### Vector Application

For a vector $\mathbf{q} = [q_0, q_1, q_2]^T$ at position $m=2$:

$$\mathbf{R}_2 \mathbf{q} = \begin{pmatrix} -0.416 & -0.909 & 0 \\ 0.909 & -0.416 & 0 \\ 0 & 0 & 1 \end{pmatrix} \begin{pmatrix} q_0 \\ q_1 \\ q_2 \end{pmatrix} = \begin{pmatrix} -0.416 q_0 - 0.909 q_1 \\ 0.909 q_0 - 0.416 q_1 \\ q_2 \end{pmatrix}$$

Note that the third component $q_2$ is unchanged by the rotation.

### Relative Position Property Preserved

Despite the unpaired dimension, the crucial relative position property still holds:

**Proposition.** For odd dimension $d$ and positions $m, n$:

$$(\mathbf{R}_m \mathbf{q})^T (\mathbf{R}_n \mathbf{k}) = \mathbf{q}^T \mathbf{R}_{n-m} \mathbf{k}$$

_Proof._ Decompose the dot product into paired and unpaired components:

$$(\mathbf{R}_m \mathbf{q})^T (\mathbf{R}_n \mathbf{k}) = \sum_{i=0}^{k-1} (\mathbf{R}_m^{(i)} \mathbf{q}_{2i:2i+1})^T (\mathbf{R}_n^{(i)} \mathbf{k}_{2i:2i+1}) + q_{d-1} k_{d-1} = \sum_{i=0}^{k-1} \mathbf{q}_{2i:2i+1}^T \mathbf{R}_{n-m}^{(i)} \mathbf{k}_{2i:2i+1} + q_{d-1} k_{d-1} = \mathbf{q}^T \mathbf{R}_{n-m} \mathbf{k}$$

where the unpaired dimension contributes $q_{d-1} k_{d-1}$ which has no positional dependence. $\square$

### Practical Considerations

**Validity:** RoPE with odd dimensions is mathematically valid and functionally correct.

**Efficiency:** The unpaired dimension receives no positional encoding, effectively "wasting" one dimension of model capacity.

**Common practice:** Attention dimensions in modern transformers are typically even (32, 64, 128, 256) for:

- Maximal utilization of positional encoding
- Computational efficiency (powers of 2)
- Convention and compatibility with existing implementations

**Use cases:** Odd dimensions might arise in:

- Experimental architectures exploring non-standard dimensions
- Legacy systems or specific architectural constraints
- Research investigating the impact of positional encoding coverage

---

## 12. 2D RoPE for Vision Transformers

While RoPE was originally designed for 1D sequences (text), it can be extended to 2D spatial data such as images in Vision Transformers (ViTs).

### The 2D Positioning Challenge

In natural language, tokens have a natural 1D ordering: position $m \in {0, 1, 2, \ldots, n-1}$.

In images processed by Vision Transformers, patches have 2D coordinates: $(h, w)$ where:

- $h \in {0, 1, \ldots, H-1}$ is the row (height) index
- $w \in {0, 1, \ldots, W-1}$ is the column (width) index

We need a rotation matrix $\mathbf{R}_{h,w}$ that encodes both spatial dimensions.

### Dimension Splitting Approach

The most common solution splits the embedding dimension to encode each spatial axis separately.

**Definition 5 (2D RoPE Rotation Matrix).** For a patch at position $(h, w)$ with embedding dimension $d$ (even), the 2D RoPE rotation matrix is:

$$\mathbf{R}_{h,w} = \begin{pmatrix} \mathbf{R}_h^{\text{row}} & \mathbf{0} \\ \mathbf{0} & \mathbf{R}_w^{\text{col}} \end{pmatrix}$$

where:

- $\mathbf{R}_h^{\text{row}} \in \mathbb{R}^{d/2 \times d/2}$ is the standard RoPE matrix for row position $h$
- $\mathbf{R}_w^{\text{col}} \in \mathbb{R}^{d/2 \times d/2}$ is the standard RoPE matrix for column position $w$

This means:

- Dimensions $0$ to $d/2-1$ encode the **row position** $h$
- Dimensions $d/2$ to $d-1$ encode the **column position** $w$

### Explicit 2D Formulation

For dimension $d=4$ with two dimension pairs, the 2D rotation matrix is:

$$\mathbf{R}_{h,w} = \begin{pmatrix} \cos(h\theta_{h,0}) & -\sin(h\theta_{h,0}) & 0 & 0 \\ \sin(h\theta_{h,0}) & \cos(h\theta_{h,0}) & 0 & 0 \\ 0 & 0 & \cos(w\theta_{w,0}) & -\sin(w\theta_{w,0}) \\ 0 & 0 & \sin(w\theta_{w,0}) & \cos(w\theta_{w,0}) \end{pmatrix}$$

where $\theta_{h,0}$ and $\theta_{w,0}$ are the frequency parameters for row and column respectively.

### Example: $d=4$, Position $(h=2, w=3)$

Using $\theta_{h,0} = \theta_{w,0} = 1$:

$$\mathbf{R}_{2,3} = \begin{pmatrix} \cos(2) & -\sin(2) & 0 & 0 \\ \sin(2) & \cos(2) & 0 & 0 \\ 0 & 0 & \cos(3) & -\sin(3) \\ 0 & 0 & \sin(3) & \cos(3) \end{pmatrix} \approx \begin{pmatrix} -0.416 & -0.909 & 0 & 0 \\ 0.909 & -0.416 & 0 & 0 \\ 0 & 0 & -0.990 & 0.141 \\ 0 & 0 & -0.141 & -0.990 \end{pmatrix}$$

**Interpretation:**

- First 2 dimensions rotated by $2$ radians (encoding row=2)
- Last 2 dimensions rotated by $3$ radians (encoding column=3)

### Vector Application in 2D

For a vector $\mathbf{q} = [q_0, q_1, q_2, q_3]^T$ at position $(2,3)$:

$$\mathbf{R}_{2,3} \mathbf{q} = \begin{pmatrix} -0.416 q_0 - 0.909 q_1 \\ 0.909 q_0 - 0.416 q_1 \\ -0.990 q_2 + 0.141 q_3 \\ -0.141 q_2 - 0.990 q_3 \end{pmatrix}$$

### Example: $d=8$ with Multi-Frequency Encoding

For $d=8$, each spatial axis receives 4 dimensions, forming 2 rotation pairs per axis. This enables multi-frequency encoding similar to the 1D case.

Using the standard frequency schedule for each axis with $i \in {0, 1}$:

$$\theta_{h,i} = 10000^{-2i/4} = 10000^{-i/2} \quad \text{(row frequencies)}$$ $$\theta_{w,i} = 10000^{-2i/4} = 10000^{-i/2} \quad \text{(column frequencies)}$$

This gives $\theta_{h,0} = \theta_{w,0} = 1$ and $\theta_{h,1} = \theta_{w,1} = 0.01$.

**Dimension allocation:**

- Dims 0–1: Row position with frequency $\theta_{h,0} = 1$ (high frequency)
- Dims 2–3: Row position with frequency $\theta_{h,1} = 0.01$ (low frequency)
- Dims 4–5: Column position with frequency $\theta_{w,0} = 1$ (high frequency)
- Dims 6–7: Column position with frequency $\theta_{w,1} = 0.01$ (low frequency)

At position $(h=2, w=3)$, numerically:

$$\mathbf{R}_{2,3} \approx \begin{pmatrix} -0.416 & -0.909 & 0 & 0 & 0 & 0 & 0 & 0 \\ 0.909 & -0.416 & 0 & 0 & 0 & 0 & 0 & 0 \\ 0 & 0 & 0.9998 & -0.0200 & 0 & 0 & 0 & 0 \\ 0 & 0 & 0.0200 & 0.9998 & 0 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 & -0.990 & 0.141 & 0 & 0 \\ 0 & 0 & 0 & 0 & -0.141 & -0.990 & 0 & 0 \\ 0 & 0 & 0 & 0 & 0 & 0 & 0.9996 & -0.0300 \\ 0 & 0 & 0 & 0 & 0 & 0 & 0.0300 & 0.9996 \end{pmatrix}$$

**Vector application** for $\mathbf{q} = [q_0, q_1, q_2, q_3, q_4, q_5, q_6, q_7]^T$:

$$\mathbf{R}_{2,3} \mathbf{q} = \begin{pmatrix} -0.416 q_0 - 0.909 q_1 \\ 0.909 q_0 - 0.416 q_1 \\ 0.9998 q_2 - 0.0200 q_3 \\ 0.0200 q_2 + 0.9998 q_3 \\ -0.990 q_4 + 0.141 q_5 \\ -0.141 q_4 - 0.990 q_5 \\ 0.9996 q_6 - 0.0300 q_7 \\ 0.0300 q_6 + 0.9996 q_7 \end{pmatrix}$$

**Interpretation:**

- **High-frequency components** (dims 0–1 for row, 4–5 for column): These rotate significantly even for small position changes, capturing fine-grained local spatial relationships.
- **Low-frequency components** (dims 2–3 for row, 6–7 for column): These rotate slowly, capturing coarse-grained long-range spatial relationships.

This multi-scale encoding is analogous to 1D RoPE and allows the model to attend to both nearby and distant patches effectively.

### Dimension Efficiency for 2D RoPE

**Optimal dimensions:** For 2D RoPE with equal split between axes, dimensions divisible by 4 are optimal:

|$d$|Dims per axis|Pairs per axis|Unutilized dims|Efficiency|
|---|---|---|---|---|
|4|2|1|0|100%|
|6|3|1|2 (total)|67%|
|8|4|2|0|100%|
|10|5|2|2 (total)|80%|
|12|6|3|0|100%|

When $d = 4k$ (divisible by 4), each axis gets $2k$ dimensions forming $k$ rotation pairs with no waste. When $d = 4k+2$, each axis gets $2k+1$ dimensions, leaving one dimension per axis unrotated (wasted).

### 2D Relative Position Property

**Proposition 2 (2D Relative Position Encoding).** For patches at positions $(h_1, w_1)$ and $(h_2, w_2)$:

$$(\mathbf{R}_{h_1,w_1} \mathbf{q})^T (\mathbf{R}_{h_2,w_2} \mathbf{k}) = \mathbf{q}^T \mathbf{R}_{\Delta h, \Delta w} \mathbf{k}$$

where $\Delta h = h_2 - h_1$ and $\Delta w = w_2 - w_1$ are the relative offsets.

_Proof._ Since $\mathbf{R}_{h,w}$ is block-diagonal with independent row and column blocks:

$$(\mathbf{R}_{h_1,w_1} \mathbf{q})^T (\mathbf{R}_{h_2,w_2} \mathbf{k}) = (\mathbf{R}_{h_1}^{\text{row}} \mathbf{q}_{\text{row}})^T (\mathbf{R}_{h_2}^{\text{row}} \mathbf{k}_{\text{row}}) + (\mathbf{R}_{w_1}^{\text{col}} \mathbf{q}_{\text{col}})^T (\mathbf{R}_{w_2}^{\text{col}} \mathbf{k}_{\text{col}}) = \mathbf{q}_{\text{row}}^T \mathbf{R}_{h_2-h_1}^{\text{row}} \mathbf{k}_{\text{row}} + \mathbf{q}_{\text{col}}^T \mathbf{R}_{w_2-w_1}^{\text{col}} \mathbf{k}_{\text{col}} = \mathbf{q}^T \mathbf{R}_{\Delta h, \Delta w} \mathbf{k}$$

where $\mathbf{q}_{\text{row}}$ denotes the first $d/2$ dimensions and $\mathbf{q}_{\text{col}}$ the last $d/2$ dimensions. $\square$

This means attention depends only on the **relative spatial offset** $(\Delta h, \Delta w)$, not absolute positions.

### Concrete Vision Transformer Example

Consider a $224 \times 224$ image divided into $16 \times 16$ patches, creating a $14 \times 14$ grid.

With $d=64$ dimensional embeddings:

- Dimensions 0–31: encode row position (32 dims for $h \in {0, \ldots, 13}$)
- Dimensions 32–63: encode column position (32 dims for $w \in {0, \ldots, 13}$)

**Patch at $(1, 2)$:**

$$\mathbf{R}_{1,2} = \begin{pmatrix} \mathbf{R}_1^{\text{row}} & \mathbf{0}_{32 \times 32} \\ \mathbf{0}_{32 \times 32} & \mathbf{R}_2^{\text{col}} \end{pmatrix}$$

**Patch at $(3, 2)$:**

$$\mathbf{R}_{3,2} = \begin{pmatrix} \mathbf{R}_3^{\text{row}} & \mathbf{0}_{32 \times 32} \\ \mathbf{0}_{32 \times 32} & \mathbf{R}_2^{\text{col}} \end{pmatrix}$$

These patches share column position $w=2$, so their column encodings (dims 32–63) are identical. The attention between them depends on row offset $\Delta h = 3 - 1 = 2$.

### Alternative Approaches

#### Axial RoPE (Interleaved)

Instead of splitting dimensions 50-50, interleave row and column encodings:

$$\mathbf{R}_{h,w}^{\text{axial}} = \begin{pmatrix} \mathbf{R}_h^{(0)} & & & & \\ & \mathbf{R}_w^{(0)} & & & \\ & & \mathbf{R}_h^{(1)} & & \\ & & & \mathbf{R}_w^{(1)} & \\ & & & & \ddots \end{pmatrix}$$

This alternates between row and column encodings at each dimension pair level.

#### Flattened 1D (Naive)

Map 2D position $(h,w)$ to 1D: $m = h \times W + w$, then apply standard 1D RoPE.

**Problem:** This breaks spatial structure. Horizontally adjacent patches have $\Delta m = 1$, but vertically adjacent patches have $\Delta m = W$, treating them as much farther apart.

#### Frequency Scaling

Use different frequency schedules for row vs column dimensions to account for different aspect ratios:

$$\theta_{h,i} = \beta_h^{-2i/(d/2)}, \qquad \theta_{w,i} = \beta_w^{-2i/(d/2)}$$

where $\beta_h, \beta_w$ can differ based on image height and width.

### Extension to 3D (Video)

For video with dimensions (height, width, time), split into three groups:

$$\mathbf{R}_{h,w,t} = \begin{pmatrix} \mathbf{R}_h^{\text{height}} & & \\ & \mathbf{R}_w^{\text{width}} & \\ & & \mathbf{R}_t^{\text{time}} \end{pmatrix}$$

Each third of the dimensions encodes one spatial-temporal axis.

### Practical Models Using 2D RoPE

**Vision-Language Models:**

- LLaVA-NeXT: Uses 2D RoPE for image patches
- Qwen-VL: Applies 2D positional encoding to vision encoder

**Pure Vision Models:**

- Some ViT variants exploring RoPE as alternative to absolute position embeddings
- MAE (Masked Autoencoder) extensions

### Advantages and Considerations

**Advantages:**

- Natural extension of 1D RoPE principles
- Maintains relative position encoding property in 2D
- No learned parameters (unlike absolute 2D position embeddings)
- Better extrapolation to different image sizes

**Considerations:**

- Dimension allocation: how many dims per axis?
- Frequency schedule: same or different for each axis?
- May not capture diagonal relationships as naturally
- Increased complexity compared to 1D case

---

## 13. Comparison with Original Position Embeddings

### Original Sinusoidal Position Embeddings

The original Transformer (Vaswani et al., 2017) uses sinusoidal position embeddings that are **added** to token embeddings:

$$\mathbf{x}_m = \mathbf{e}_m + \mathbf{p}_m$$

where the position embedding $\mathbf{p}_m$ is defined as:

$$\mathbf{p}_m[2i] = \sin\left(\frac{m}{10000^{2i/d}}\right), \qquad \mathbf{p}_m[2i+1] = \cos\left(\frac{m}{10000^{2i/d}}\right)$$

Attention is then computed on the combined representation: $\text{score} = (\mathbf{e}_m + \mathbf{p}_m)^T \mathbf{W}_Q \mathbf{W}_K^T (\mathbf{e}_n + \mathbf{p}_n)$.

### Key Differences

|Aspect|Original PE|RoPE|
|---|---|---|
|Position encoding|Absolute positions|Relative positions|
|Application|Added to embeddings|Rotates $\mathbf{q}$, $\mathbf{k}$ in attention|
|Content mixing|Mixes position with content early|Keeps content and position separate|
|Dependencies|Score depends on $m$ and $n$ separately|Score depends only on $n-m$|
|Extrapolation|Poor beyond training length|Good extrapolation to longer sequences|
|Theoretical basis|Heuristic design|Rotation group theory|

### Main Benefits of RoPE

1. **Relative position encoding:** The crucial property $(\mathbf{R}_m \mathbf{q})^T (\mathbf{R}_n \mathbf{k}) = \mathbf{q}^T \mathbf{R}_{n-m} \mathbf{k}$ means attention naturally depends on relative offset $n-m$ rather than absolute positions. This better matches the structure of language where relationships like "subject-verb" depend on relative distance, not absolute position in the sequence.
2. **Length extrapolation:** Models trained on sequences of length $L$ can handle sequences of length $L' \gg L$ with minimal performance degradation. Since the model learns relative patterns ("attend 3 tokens back"), these patterns transfer to unseen absolute positions. In practice, RoPE models can extrapolate to 2–4× training length.
3. **Separation of concerns:** Token embeddings remain pure semantic representations. Position information is only introduced within the attention mechanism, avoiding "pollution" of the embedding space with positional artifacts.
4. **Superior empirical performance:** RoPE consistently achieves better perplexity and downstream task performance compared to sinusoidal PE, especially on long-context tasks. This has led to its adoption in modern LLMs including LLaMA, PaLM, GPT-NeoX, and Qwen.

### Why Relative Position Matters

Consider the phrase "The cat sat on the mat" appearing at different locations in a document:

**With absolute position encoding:** The relationship between "cat" (position 1) and "sat" (position 2) must be relearned when the same phrase appears at positions 10 and 11, or positions 100 and 101.

**With RoPE:** The model learns that the subject appears 1 token before the verb. This relative pattern applies universally, regardless of absolute position in the sequence.

### Current Status

RoPE has become the de facto standard for position encoding in state-of-the-art language models, effectively replacing both sinusoidal and learned absolute position embeddings. The combination of theoretical elegance, practical benefits, and strong empirical results has established RoPE as a fundamental component of modern transformer architectures.

---
## 14. Conclusion

The mapping $m \mapsto \mathbf{R}_m$ provides an elegant way to inject positional information into transformer attention through rotations. The key properties—orthogonality, group structure, and the relative position encoding property—make RoPE both theoretically principled and practically effective for modern large language models. The extension to 2D positional encoding demonstrates RoPE's versatility, enabling applications beyond sequential data to spatial domains such as computer vision.