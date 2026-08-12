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
- **2026-08** — Training code, evaluation scripts, and the pretrained AGFlow checkpoint are released.

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

## 📁 Repository Structure

```
AGFlow/
├── run_train_anytime_v4_flow.py   # Training entry point (flow matching)
├── run_eval.py                    # Evaluation entry point
├── run_eval_parallel.sh           # Parallel evaluation over patches
├── FlowMatchingScheduler.py       # Linear flow matching: x_t = (1−t)·x₀ + t·ε,  v = ε − x₀
├── SeqFlowPipeline.py             # Euler ODE sampling with observed-pixel clamping
├── explore_anytime_pt_free.py     # Anytime generation demo / visualization
├── requirements.txt
├── configs/
│   ├── config_PASTIS_anytime_train_flow.yaml   # Training config (paper settings)
│   ├── config_paper_run_exact.yaml             # Exact auto-saved config of the paper run
│   └── default_train.yaml                      # Defaults (merged with the above)
└── lib/
    ├── models/SDT_4.py            # Sequential Denoising Transformer
    │                              #   • Time-Aligned Cross-Attention (spatial + temporal)
    │                              #   • Learned relative-time bias (bucketed day differences)
    │                              #   • RoPE over real acquisition dates
    │                              #   • Absolute + query-relative date embeddings
    ├── trainer_anytime_v4_flow.py # Flow-matching trainer (masked-pixel MSE loss)
    ├── eval_tools_flow.py         # Imputation / evaluation with flow sampling
    ├── datasets/PASTISDataset.py  # PASTIS-R data loading
    └── metrics.py                 # MAE / RMSE / SAM / PSNR / SSIM (masked)
```

---

## 🔧 Installation

```bash
git clone https://github.com/AGFlow-model/AGFlow.git
cd AGFlow
conda create -n agflow python=3.10 -y
conda activate agflow
pip install -r requirements.txt
```

Tested with Python 3.10 and PyTorch ≥ 2.0 (CUDA).

---

## 🛰️ Dataset & Setup

- **Target sensor:** Sentinel-2 optical time series (10 spectral bands, atmospheric bands excluded)
- **Condition sensor:** Sentinel-1 SAR time series (VV, VH, VV/VH; ascending orbit)
- **Benchmark:** RESTORE-DiT evaluation protocol
- **Region:** France site from **PASTIS-R** (tiles 30UXV, 32ULU, 31TFM, 31TFJ; 2433 patches of 128×128 px)
- **S2 observations:** **38–61 acquisitions per series**
- **S1 observations:** about **70 acquisitions per series**

**Setup steps:**

1. Download **PASTIS-R** from the [official repository](https://github.com/VSainteuf/pastis-benchmark).
2. Point the config to your data location:

```yaml
# configs/config_PASTIS_anytime_train_flow.yaml
data:
    root: /path/to/PASTIS-R
```

We use the official 5-fold split (four folds for training, one for testing).

---

## 📦 Pretrained Models

| Model | Task | MAE ↓ | RMSE ↓ | Download |
|---|---|---|---|---|
| AGFlow (full) | Missing-frame reconstruction | 0.0179 | 0.0261 | 👉 [LINK] |
| AGFlow (full) | Cloud removal | 0.0133 | 0.0217 | *(same checkpoint)* |

```bash
mkdir -p checkpoints
# download AGFlow_full_best.pth into ./checkpoints/
md5sum checkpoints/AGFlow_full_best.pth
# expected: 35407ee6323c7d5d56bef4d3dc82dae8
```

---

## 🚀 Training

```bash
python run_train_anytime_v4_flow.py configs/config_PASTIS_anytime_train_flow.yaml
```

Paper settings (Sec. 4.2): SDT with depth 4, hidden size 256, patch size 4×4, 4 attention heads, MLP ratio 4.0, temporal window T = 15, 128×128 crops; Adam (lr 2×10⁻⁴), batch size 16, 3000 epochs, MultiStepLR schedule. Training uses flow matching with τ ~ U(0,1), linear interpolation y_τ = (1−τ)y + τε, and target velocity ε − y, with the loss computed on masked pixels only.

Weights & Biases logging is on by default (`--wandb_project` to change the project name).

---

## 📊 Evaluation

**Cloud removal / missing-frame reconstruction:**

```bash
python run_eval.py \
    --config configs/config_paper_run_exact.yaml \
    --checkpoint ./checkpoints/AGFlow_full_best.pth \
    --inference_steps 50 \
    --out_dir ./eval_outputs
```

**Anytime generation at a user-specified date:**

```bash
python run_eval.py \
    --config configs/config_paper_run_exact.yaml \
    --checkpoint ./checkpoints/AGFlow_full_best.pth \
    --task_mode anytime_nn \
    --target_date 2019-05-29 \
    --reference_date 2018-09-01 \
    --inference_steps 50
```

For evaluating many patches in parallel, see `run_eval_parallel.sh`. Metrics (MAE, RMSE, SAM, PSNR, SSIM) are computed on missing/occluded pixels only, following the RESTORE-DiT protocol.

---

## 📈 Results

**Missing-frame (gap filling) reconstruction** — one S2 frame fully removed and reconstructed from the remaining temporal context:

| Model | MAE ↓ | RMSE ↓ | SAM ↓ | PSNR ↑ | SSIM ↑ |
|---|---|---|---|---|---|
| RESTORE-DiT | 0.0214 | 0.0322 | 2.95 | 32.17 | 0.914 |
| **AGFlow (full)** | **0.0179** | **0.0261** | **2.78** | **32.87** | **0.942** |

**Cloud removal** — France test set, metrics over all ten S2 bands on cloud-corrupted pixels:

| Method | MAE ↓ | RMSE ↓ | SAM ↓ | PSNR ↑ | SSIM ↑ |
|---|---|---|---|---|---|
| Linear | 0.0257 | 0.0401 | 4.35 | 28.40 | 0.929 |
| U-TILISE | 0.0202 | 0.0314 | 3.76 | 30.38 | 0.936 |
| U-TILISE-SAR | 0.0193 | 0.0298 | 3.66 | 30.77 | 0.937 |
| RESTORE-DiT | 0.0140 | 0.0224 | 2.64 | 33.32 | 0.959 |
| **AGFlow (ours)** | **0.0133** | **0.0217** | **2.45** | **33.65** | **0.964** |

---

## 🌱 Anytime Generation

AGFlow also supports **query-time generation**:

- Users can ask for a cloud-free Sentinel-2 frame at an **unobserved date**
- The paper evaluates this using **NDVI trend agreement** against an auxiliary cloud-free reference from **RapidAI4EO**
- The goal is not exact pixel matching, but **plausible seasonal dynamics and regional consistency**

A visualization example is provided in `explore_anytime_pt_free.py` (sample outputs in `explored/`).

---

## 🗺️ Roadmap

- [x] Release training code
- [x] Release evaluation scripts
- [x] Upload pretrained checkpoints
- [x] Provide reproducibility environment file
- [ ] Publish model card and usage notes
- [ ] Add demo notebook for anytime querying

---

## ⚖️ License

This project is released under the [MIT License](./LICENSE).

---

## ✉️ Contact & Acknowledgments

- **Contact:** wenwen@asu.edu

### Acknowledgments

This research is supported in part by **Google.org's Impact Challenge for Climate Innovation Program** and the **National Science Foundation under award 2120943**.

We also thank **Research Computing (RC) at Arizona State University** for their support in providing computing resources.

We further thank the authors of <a href="https://github.com/SQD1/RESTORE-DiT">RESTORE-DiT</a> and <a href="https://github.com/tangkai-RS/AnytimeFormer">AnytimeFormer</a> for their valuable prior work on satellite time-series reconstruction, asynchronous fusion, and anytime querying.

## 📖 Citation

If you find AGFlow useful in your research, please cite:

```bibtex
@inproceedings{fallah2026asynchronous,
  title={Asynchronous remote sensing time-series fusion for cloud removal and anytime reconstruction},
  author={Fallah, Forouzan and Hsu, Chia-Yu and Li, Wenwen and Liljedahl, Anna and Yang, Yezhou},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={7772--7780},
  year={2026}
}
```
