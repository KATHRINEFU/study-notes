---
title: "Stanford CS229(2018 fall) class notes"
seoTitle: "Stanford machine learning cs229  course note"
datePublished: Tue Aug 26 2025 05:47:04 GMT+0000 (Coordinated Universal Time)
cuid: cmes4j2uh000002ib2xvc9tes
slug: stanford-cs2292018-fall-class-notes-1
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/M_vaMr-2P7o/upload/b524b002157621e110ef14542ec0d5be.jpeg
tags: machine-learning, computer-science, stanford, study

---

> [https://see.stanford.edu/Course/CS229](https://see.stanford.edu/Course/CS229)

### Supervised learning

Given a training set, to learn a function `h : X → Y` so that `h(x)` is a “good” predictor for the corresponding value of `y`.

regression problem (y is continuous) vs classification problem (y is discrete)

### Linear regression

present `hθ(x) = θ0 + θ1x1 + θ2x2`, also

$$h(x) = \sum^n_{i=0}\theta_ix_i$$

where `θ` (parameters) and `x` (inputs/features) both as vectors, and here `n` = # of features (not counting `x0`), `x0` = 1. `M` = # of training samples (rows); `(x, y)` = training example; `(x_i, y_i)` = `i_th` training example (i range from 1-M).

target/ **cost function**: choose `θ` st. `h(x) → y`, for the training examples, minimize

$$J(\theta) = \frac{1}{2}\sum^m_{i=1}(h_{\theta}(x^{(i)})-y^{(y)})^2$$

(putting ½ here to let math easier for calculus)

### Gradient descent

start with initial `θ`, keep changing `θ` to refine `J(θ)`, finding the steepest path downward.

$$\theta_j := \theta_j - \alpha\frac{\partial}{\partial \theta_j} J(\theta)$$

`α`: learning rate, derivative of `J(θ)`, where

$$\frac{\partial}{\partial \theta_j} J(\theta) = (h_\theta(x) - y) \times x_j$$

plug it in, for all features

$$\theta_j := \theta_j - \alpha \sum^M_{i=1}(h_\theta(x^{(i)}-y^{(i)})x_j^{(i)}, (for\space j=0, 1, ..., n)$$

repeat until convergence. also called **batch** **gradient descent**: looks at every example in the entire training set on every step. disadvantage: for a giant dataset, in order to make one update, needs scan through the entire the dataset to calculate the sum.

**stochastic gradient descent**, a slight more random, chaotic path

$$for \space i=1, 2, ... M, \theta_j := \theta_j - \alpha (h_\theta(x^{(i)}-y^{(i)})x_j^{(i)}, (for\space j=0, 1, ..., n)$$

update the parameters according to the gradient of the error with respect to one single training example.

can also switch them together or mini batch descent (eg. 1/10 of dataset once), decrease learning rate to have smaller stabelized steps when it’s closing.

### Normal equation

only works for linear regression

pre knowledge of linear algebra: `Tr(AB) = Tr(BA)`, `Tr(A) = Tr(A^T)`, `Tr(A+B) = Tr(A) + Tr(B)`, `Tr(aA) = aTr(A)`，`∇A Tr(AB) = B^T`, `∇A Tr(AA^TC) = CA + C^TA`, `A^TA = sum^(i)(Z^i)²`

$$J(\theta) = \frac{1}{2}\sum^m_{i=1}(h_{\theta}(x^{(i)})-y^{(y)})^2 = \frac{1}{2}(X\theta-y)^T(X\theta -y)$$

therefore, to find the `θ` that minimize `J(θ)`: take derivative with respect to `θ`:

$$\nabla_\theta J(\theta) = \nabla \frac{1}{2}(X\theta-y)^T(X\theta -y) \\ = \frac{1}{2}\nabla_\theta[(\theta^T X^TX\theta - \theta^T X^T\vec{y} - \vec{y}X\theta+\vec{y}^T\vec{y} = X^TX\theta - X^T\vec{y}$$

To minimize `J`, set its derivatives to zero, and obtain the normal equation:

$$\theta = (X^TX)^{-1}X^T\vec{y}$$

### reflections:

1. linear algebra to review!