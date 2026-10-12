# User Guide (high level)

## Environment
- CPU: multi-core Xeon/Ryzen; RAM ≥ 32 GB (16 GB for light inference); GPU: CUDA-capable, ≥ 16 GB VRAM recommended (A100/H100/V100 for training); ≥ 100 GB SSD
- CUDA 11.8 or 12.1, PyTorch GPU build, Python 3.10, Conda

## Install
```bash
conda create -n beatnet python=3.10 && conda activate beatnet
pip install -U pip setuptools wheel
pip install numpy scipy pandas matplotlib scikit-learn tqdm wfdb pyyaml onnxruntime
git clone https://github.com/Jwoo5/fairseq-signals.git && cd fairseq-signals && pip install -e .
```
Place checkpoints in `models/ecgfm/` and `models/delineation/`.

## Configure
See [`configs/example_config.yaml`](../configs/example_config.yaml): lead selection (Lead II preferred, Lead I fallback), window/stride (30 s recommended; 5 s for low latency), per-class thresholds.

## Run
```bash
python run_beatnet.py --input ecg.dat --lead II
```

## Reading the output
| Confidence | Meaning |
|---|---|
| 0.80–1.00 | Strong |
| 0.60–0.79 | Moderate |
| 0.40–0.59 | Borderline — review suggested |
| < 0.40 | Low / not triggered |

PQRST overlays show P onset/peak, Q/R/S, T peak/offset, beat intervals and heart rate. Saliency maps (PVC: widened beats; LBBB: prolonged QRS; tachycardia: short RR; AFib: irregular rhythm) are interpretability aids, not standalone evidence.

## Real-time demo mode
Receives live waveform packets from an Aventusoft device and runs sliding-window inference with live classification, heart rate and PQRST overlays.
Demo: <https://ippd-ca.politeforest-729c8e80.eastus.azurecontainerapps.io>

## Troubleshooting
| Symptom | Cause | Fix |
|---|---|---|
| CUDA not detected | Driver / toolkit mismatch | Reinstall matching driver and PyTorch CUDA build |
| GPU out of memory | Batch too large | Reduce batch size, use mixed precision |
| fairseq-signals not found | Not installed editable | `pip install -e .` in the clone |
| Slow inference | CPU only | Use GPU or fewer windows |
| Missing weights | Wrong path | Check model dir and config |
| Low infarction confidence | Single-lead limit | Use Lead II |
