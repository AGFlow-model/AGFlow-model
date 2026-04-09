# ☁️ AGFlow: Asynchronous Remote Sensing Time-Series Fusion for Cloud Removal and Anytime Reconstruction

<p align="left">
  <!-- Optional: replace with your real links -->
  <a href=" ">
    <img alt="Project page" src="https://img.shields.io/badge/Project%20page-visit-blue">
  </a>
  <a href=" ">
    <img alt="Paper" src="https://img.shields.io/badge/Paper-PDF-B31B1B">
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
- **TBA** — code and pretrained weights release.
- **TBA** — project page and demo.

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
