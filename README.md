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

This lets the model preserve observed pixels while generating only the missing content.

---

## 🛰️ Dataset & Setup

- **Target sensor:** Sentinel-2 optical time series
- **Condition sensor:** Sentinel-1 SAR time series
- **Benchmark:** RESTORE-DiT evaluation protocol
- **Region:** France site from **PASTIS-R**
- **Patch size:** `128 x 128`
- **Temporal span:** **Sep 2018 – Nov 2019**
- **S2 observations:** **38–61 acquisitions per series**
- **S1 observations:** about **70 acquisitions per series**
- **Window length:** `15`

Preprocessing used in the paper:

- Sentinel-2 clipped to a fixed reflectance range
- Sentinel-1 standardized with dataset statistics
- Real cloud masks sampled from the benchmark mask pool

---

## 📊 Main Results

### Missing-frame reconstruction

| Model | MAE ↓ | RMSE ↓ | SAM ↓ | PSNR ↑ | SSIM ↑ |
|------|------:|------:|------:|------:|------:|
| RESTORE-DiT | 0.0214 | 0.0322 | 2.9514 | 32.1755 | 0.9139 |
| **AGFlow** | **0.0179** | **0.0261** | **2.7761** | **32.8671** | **0.9420** |

### Cloud removal

| Model | MAE ↓ | RMSE ↓ | SAM ↓ | PSNR ↑ | SSIM ↑ |
|------|------:|------:|------:|------:|------:|
| Linear | 0.0257 | 0.0401 | 4.35 | 28.40 | 0.929 |
| U-TILISE | 0.0202 | 0.0314 | 3.76 | 30.38 | 0.936 |
| U-TILISE-SAR | 0.0193 | 0.0298 | 3.66 | 30.77 | 0.937 |
| RESTORE-DiT | 0.0140 | 0.0224 | 2.64 | 33.32 | 0.959 |
| **AGFlow** | **0.0133** | **0.0217** | **2.45** | **33.65** | **0.964** |

---

## 🔍 Ablation Highlights

The paper shows that AGFlow performs best when all parts are used together:

- Removing **time-aligned SAR fusion** causes the largest drop
- Removing **relative time bias** also hurts performance
- Removing **query-relative timing** gives a smaller but still consistent drop

This supports that **temporal alignment inside the model** is important for both fidelity and spectral consistency.

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
- [ ] Add data preparation instructions
- [ ] Publish model card and usage notes
- [ ] Provide reproducibility environment file
- [ ] Add demo notebook for anytime querying

---

## ⚠️ Limitations

- Assumes good spatial co-registration between Sentinel-1 and Sentinel-2
- Uses fixed-length temporal windows, so very long dependencies may be only partly captured
- May require retraining or calibration for other sensors, regions, or processing conventions

---

## 📝 Citation

If you find this work useful, please cite:

```bibtex
@article{fallah2026agflow,
  title   = {Asynchronous Remote Sensing Time-Series Fusion for Cloud Removal and Anytime Reconstruction},
  author  = {Fallah, Forouzan and Hsu, Chia-Yu and Li, Wenwen and Liljedahl, Anna and Yang, Yezhou},
  year    = {2026}
}
