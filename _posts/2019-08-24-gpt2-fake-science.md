---
layout: post
title:  "GPT-2 Fake Science"
date:   2019-08-24
---

Text generation has gone a long way this past year! With the initial release of the small 124M parameter GPT-2 model in February 2019 to the most recent larger 774M parameter version, now it is easier-than-ever to produce realistic-looking text with paragraph-to-paragraph coherency. Before the state-of-the-art in text generation only had coherency within a sentence or two, but not more.

GPT-2 does remarkably well at generating news articles-length of text. Unfortunately, longer-length text like academic papers or even novels do not maintain sufficient coherency. Regardless of this fact, I thought it would be fun to generate a scientific paper. I took 2.5k LaTeX source files on Chaos from [arXiv](https://arxiv.org/) and fed it into a fine-tuning program for GPT-2.

Here is the result:
[Nonlinear turbulence following random forced harmonic oscillators](/assets/Nonlinear turbulence following random forced harmonic oscillators.pdf)
