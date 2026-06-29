---
title: "DiffFake: Difficulty-Aware Data Selection for AI-Generated Image Detection"
date: 2026-06-29
excerpt: "A project note on using Local Pixel Dependency statistics and Mahalanobis distance to identify hard real and fake samples for synthetic image detection."
permalink: /projects/difffake/
author_profile: false
image: /images/projects/difffake/ferretnet-overview.png
project_year: 2025
project_type: "Computer Vision"
tags:
  - Deepfake Detection
  - FerretNet
  - Data Selection
  - LPD
---

<section class="blog-hero">
  <p class="blog-eyebrow">Project Blog / Computer Vision</p>
  <h1>DiffFake: Difficulty-Aware Data Selection for AI-Generated Image Detection</h1>
  <p>
    Using Local Pixel Dependency statistics and Mahalanobis distance to identify hard real and fake samples for synthetic image detection.
  </p>
  <div class="blog-actions">
    <a href="https://github.com/Peng-Cui-2003/DiffFake">GitHub</a>
    <a href="/files/projects/difffake/report.pdf">Report PDF</a>
    <a href="/#-projects">All Projects</a>
  </div>
</section>

<figure class="blog-feature">
  <img src="/images/projects/difffake/ferretnet-overview.png" alt="FerretNet overview">
</figure>

AI-generated images are becoming increasingly realistic, which makes synthetic image detection less a question of whether a model can find obvious artifacts and more a question of whether it can still generalize when the artifacts are subtle. In this project, we revisited **FerretNet**, a lightweight detector built around Local Pixel Dependency (LPD) feature maps, and explored a difficulty-aware data selection strategy for deep fake image detection.

The project repository is available at [Peng-Cui-2003/DiffFake](https://github.com/Peng-Cui-2003/DiffFake). The full presentation report is also available here: [Report PDF](/files/projects/difffake/report.pdf).

## Motivation

FerretNet shows that LPD feature maps expose local artifacts more effectively than raw images. The original ablation results are strong, but they leave a follow-up question: if LPD really reveals synthetic artifacts, can we describe that effect statistically rather than only through end-to-end detector accuracy?

Our working assumption was:

> If LPD feature maps expose more artifacts, then simple statistical measurements computed on LPD maps should separate real and fake images better than the same measurements computed on original images.

This led us to a second question: can these statistical measurements also tell us which samples are difficult for a detector?

## Method

For each image, we compute an LPD map by subtracting a local-neighborhood reconstruction from the original image. We then extract 21 statistical features from the LPD map, covering basic statistics, spatial patterns, distribution shape, frequency-domain behavior, texture, and energy concentration.

We evaluated the features with Cohen's d, Mann-Whitney U tests, and logistic regression, then selected 8 indicators that were most useful for real/fake separation:

`glcm_contrast`, `dct_low_freq_ratio`, `min`, `max`, `energy_top20_ratio`, `autocorr`, `grad_std`, `loc_var_std`

Using only real images, we fit the mean vector and covariance matrix of these 8 LPD-derived features. Each sample is then scored by its Mahalanobis distance to the real-image feature distribution:

$$
D(x) = \sqrt{(x - \mu_{real})^T \Sigma_{real}^{-1}(x - \mu_{real})}
$$

This gives a simple definition of sample difficulty:

- **Hard real samples**: real images with large distance from the real-image distribution, because they look unusually fake-like.
- **Hard fake samples**: fake images with small distance from the real-image distribution, because they look unusually real-like.

We used this score to select the hardest 20% of samples and compared it with random sampling and CLIP-embedding diversity sampling.

## Results

On the ForenSynth test set, training with only the difficulty-selected 20% subset reached **95.4 ACC / 98.9 AP** on average, which was close to the full-data result of **95.8 ACC / 99.1 AP**. It also clearly outperformed random 20% sampling and diversity-based 20% sampling.

![ForenSynth results](/images/projects/difffake/results-forensynths.png)

On Diffusion-6-class, the out-of-domain setting was harder, but the difficulty-selected subset still performed substantially better than the two 20% baselines: **90.4 ACC / 98.1 AP**, compared with **83.1 ACC / 93.9 AP** for random sampling and **79.5 ACC / 90.0 AP** for diversity sampling.

![Diffusion results](/images/projects/difffake/results-diffusion.png)

These results suggest that the LPD-based difficulty metric is not just a descriptive statistic. It captures useful training signal: a small subset of difficult samples can retain much of the full-data performance in-domain and improve over simple subset selection strategies out-of-domain.

## Takeaways

This project made three main contributions:

- We ran fairer ablations with parameter-controlled model comparisons, showing that FerretNet's advantage is not only a model-size artifact.
- We analyzed LPD from a statistical perspective and found that LPD-derived features distinguish real and fake images more effectively than raw-image features.
- We used those features to define sample difficulty and built a data selection strategy around it.

The main limitation is that the method was tested on a limited set of training data. It is also more reliable in-domain than out-of-domain, so future work should evaluate the score across more datasets and generative model families.
