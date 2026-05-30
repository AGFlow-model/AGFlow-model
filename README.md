# ☁️ AGFlow: Asynchronous Remote Sensing Time-Series Fusion for Cloud Removal and Anytime Reconstruction

<p align="left">
  <a href="https://agflow-model.github.io/">
    <img alt="Project Page" src="https://img.shields.io/badge/Project%20Page-Visit-blue">
  </a>
  <a href="https://arxiv.org/abs/2605.27726">
    <img alt="arXiv" src="https://img.shields.io/badge/arXiv-2605.27726-B31B1B">
  </a>
  <a href="https://openaccess.thecvf.com/content/CVPR2026W/MORSE/html/Fallah_Asynchronous_Remote_Sensing_Time-Series_Fusion_for_Cloud_Removal_and_Anytime_CVPRW_2026_paper.html">
    <img alt="CVPRW 2026" src="https://img.shields.io/badge/CVPRW-2026-4B44CE">
  </a>
  <img alt="Task" src="https://img.shields.io/badge/task-cloud_removal%20%2B%20anytime_reconstruction-0a7ea4">
  <img alt="Sensors" src="https://img.shields.io/badge/Sensors-Sentinel--1%20%2F%20Sentinel--2-4c9f70">
  <a href="./LICENSE">
    <img alt="License" src="https://img.shields.io/badge/license-MIT-informational">
  </a>
</p>

> **TL;DR**: AGFlow is a timestamp-conditioned spatiotemporal flow-matching framework for asynchronous Sentinel-1 / Sentinel-2 fusion, designed for cloud removal, missing-frame reconstruction, and anytime optical image generation.

---

## 🗞️ News
🎉 AGFlow is accepted to CVPR 2026 MORSE Workshop.
- **TBA** — code and pretrained weights release.


---

## ⭐ Highlights
- **Internal timestamp-conditioned alignment** for irregular and asynchronous S1/S2 fusion.
- **Spatiotemporal flow matching** that models spatial structure and temporal dynamics jointly.
- **Anytime querying** to generate cloud-free Sentinel-2 frames at both observed and user-specified timestamps.
- **Strong missing-frame reconstruction**, reducing **MAE by 16.4%** and **RMSE by 18.9%** over RESTORE-DiT.
- **Competitive cloud removal**, with consistent gains in **MAE, RMSE, SAM, PSNR, and SSIM** over strong baselines.

---

## 🌍 Task Overview

AGFlow addresses a practical remote sensing problem:

- **Input:** irregular Sentinel-2 optical time series with clouds, shadows, or missing pixels
- **Condition:** co-registered Sentinel-1 SAR time series
- **Output:** cloud-free Sentinel-2 reconstructions across time
- **Supports:**
  - **Cloud / sensor-gap removal**
  - **Missing-frame reconstruction**
  - **Anytime generation** at user-specified dates within the monitoring window

Unlike many prior methods, AGFlow does **not** require external nearest-date pairing between Sentinel-1 and Sentinel-2.

---

## 🧠 Method Overview

AGFlow uses a **Sequential Denoising Transformer (SDT)** with:

- **Masked flow matching** for reconstruction
- **Time-aligned cross-attention** for SAR-optical fusion
- **Relative temporal bias + real-date encoding** for irregular-time modeling
- **Unified masking formulation** for both partial inpainting and full-frame generation

---

## 🛰️ Dataset & Setup

- **Target sensor:** Sentinel-2 optical time series
- **Condition sensor:** Sentinel-1 SAR time series
- **Benchmark:** RESTORE-DiT evaluation protocol
- **Region:** France site from **PASTIS-R**
- **S2 observations:** **38–61 acquisitions per series**
- **S1 observations:** about **70 acquisitions per series**

---


## 🌱 Anytime Generation

AGFlow also supports **query-time generation**:

- Users can ask for a cloud-free Sentinel-2 frame at an **unobserved date**
- The paper evaluates this using **NDVI trend agreement** against an auxiliary cloud-free reference from **RapidAI4EO**
- The goal is not exact pixel matching, but **plausible seasonal dynamics and regional consistency**

---

## 🗺️ Roadmap

- [ ] Release training code
- [ ] Release evaluation scripts
- [ ] Upload pretrained checkpoints
- [ ] Publish model card and usage notes
- [ ] Provide reproducibility environment file
- [ ] Add demo notebook for anytime querying

---
## ✉️ Contact & Acknowledgments

- **Contact:** ffallah@asu.edu

### Acknowledgments

This research is supported in part by **Google.org’s Impact Challenge for Climate Innovation Program** and the **National Science Foundation under award 2120943**.

We also thank **Research Computing (RC) at Arizona State University** for their support in providing computing resources.

We further thank the authors of <a href="https://github.com/SQD1/RESTORE-DiT">RESTORE-DiT</a> and <a href="https://github.com/tangkai-RS/AnytimeFormer">AnytimeFormer</a> for their valuable prior work on satellite time-series reconstruction, asynchronous fusion, and anytime querying.
