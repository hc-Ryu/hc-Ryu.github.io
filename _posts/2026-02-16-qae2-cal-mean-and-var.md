---
layout: post
read_time: true
show_date: true
title: "Quantum Amplitude Estimation 2; calculate mean and variance"
date: 2026-02-16
img: posts/20260114/quantum-computing.jpg
tags: [quantum computing, quantum amplitude estimation]
category: quantum information
author: Hoechang Ryu
description: "How to calculate mean and variance by quantum computing?"
mathjax: yes
toc: yes
---

[논문 읽기: Quantum mapping algorithm... (Springer)](https://link.springer.com/article/10.1007/s00158-025-04085-w){:target="_blank"}

Key words: Quantum Amplitude Estimation, <p style="text-align:center">\(<br>
\begin{align}<br>
\begin{split}<br>
m_t &= \beta_1 m_{t-1} + (1 - \beta_1) g_t \\<br>
v_t &= \beta_2 v_{t-1} + (1 - \beta_2) g_t^2<br>
\end{split}<br>
\end{align}<br>
\)</p>


