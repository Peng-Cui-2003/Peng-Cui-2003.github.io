---
title: "DiffFake: Difficulty-Aware Data Selection for AI-Generated Image Detection"
date: 2026-06-29
excerpt: "A project note on using Local Pixel Dependency statistics and Mahalanobis distance to identify hard real and fake samples for synthetic image detection."
permalink: /projects/difffake/
author_profile: false
project_year: 2025
project_type: "Computer Vision"
tags:
  - Deepfake Detection
  - FerretNet
  - Data Selection
  - LPD
---

<article class="project-detail">
  <header class="project-detail__header">
    <h1>DiffFake: Difficulty-Aware Data Selection for AI-Generated Image Detection</h1>
    <p class="project-detail__authors">Peng Cui</p>
    <p class="project-detail__venue">HKUST AI-Generated Image Detection Project, 2025</p>
    <nav class="project-detail__links" aria-label="Project links">
      <a href="https://github.com/Peng-Cui-2003/DiffFake">GitHub</a>
      <a href="/files/projects/difffake/report.pdf">Report PDF</a>
      <a href="/#-projects">Projects</a>
    </nav>
  </header>

  <section>
    <h2>One-liner summary</h2>
    <p class="project-detail__lead">
      Local Pixel Dependency statistics can be used to identify hard real and fake samples for training AI-generated image detectors.
    </p>
  </section>

  <section>
    <h2>Motivation</h2>
    <p>
      AI-generated images are becoming increasingly realistic, so synthetic image detection is no longer only about finding obvious visual artifacts. A detector also needs to learn from samples where the difference between real and fake images is subtle.
    </p>
    <p>
      This project revisits FerretNet and asks a more diagnostic question: if Local Pixel Dependency (LPD) feature maps expose synthetic artifacts, can we describe that effect with simple statistics and use it to select more informative training samples?
    </p>
  </section>

  <section>
    <h2>Method</h2>
    <p>
      For each image, I compute an LPD map by subtracting a local-neighborhood reconstruction from the original image. I then extract 21 statistical features from the LPD map and evaluate their real/fake discriminative power with Cohen's d, Mann-Whitney U tests, and logistic regression.
    </p>
    <p>
      The final difficulty score uses 8 selected LPD-derived features:
    </p>
    <p class="feature-list">
      <span>glcm_contrast</span>
      <span>dct_low_freq_ratio</span>
      <span>min</span>
      <span>max</span>
      <span>energy_top20_ratio</span>
      <span>autocorr</span>
      <span>grad_std</span>
      <span>loc_var_std</span>
    </p>
    <p>
      Using only real images, I estimate the mean vector and covariance matrix of these features. Each sample is scored by its Mahalanobis distance to the real-image feature distribution:
    </p>
    <p class="project-formula">
      D(x) = sqrt((x - mu_real)^T Sigma_real^-1 (x - mu_real))
    </p>
    <p>
      Hard real samples are real images with large distance from the real-image distribution. Hard fake samples are fake images with small distance from that distribution. The selected training subset contains the hardest samples under this definition.
    </p>
  </section>

  <section>
    <h2>Results</h2>
    <p>
      I compared difficulty-aware 20% sampling with random 20% sampling and CLIP-diversity 20% sampling. The difficulty-selected subset was close to full-data training on the in-domain benchmark and clearly stronger than the two subset baselines.
    </p>
    <table>
      <thead>
        <tr>
          <th>Setting</th>
          <th>Full data</th>
          <th>Random 20%</th>
          <th>Diversity 20%</th>
          <th>Difficulty 20%</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>ForenSynth mean</td>
          <td>95.8 ACC / 99.1 AP</td>
          <td>88.4 ACC / 93.0 AP</td>
          <td>90.6 ACC / 95.4 AP</td>
          <td><strong>95.4 ACC / 98.9 AP</strong></td>
        </tr>
        <tr>
          <td>Diffusion-6 mean</td>
          <td>96.3 ACC / 99.0 AP</td>
          <td>83.1 ACC / 93.9 AP</td>
          <td>79.5 ACC / 90.0 AP</td>
          <td><strong>90.4 ACC / 98.1 AP</strong></td>
        </tr>
      </tbody>
    </table>
  </section>

  <section>
    <h2>Takeaways</h2>
    <p>
      The main result is that LPD-derived statistics are not only useful for explaining real/fake separation; they also provide a practical signal for selecting difficult training samples. A small subset selected by this score can preserve much of the full-data performance in-domain and outperform random or diversity-based subset selection out-of-domain.
    </p>
    <p>
      The current limitation is that the method was tested on a limited set of training data. A stronger follow-up would evaluate whether the same difficulty score transfers across more datasets and generative model families.
    </p>
  </section>
</article>
