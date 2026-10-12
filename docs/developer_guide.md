# Developer Guide (high level)

## Adding a new label
1. Update `label_mapping.json`
2. Extract matching labels from MIMIC-IV-ECG or sponsor data
3. Expand the classification head
4. Rebuild train/val/test manifests
5. Re-fine-tune the ECG-FM checkpoint
6. Recalibrate thresholds; evaluate AUROC/F1

## Retraining ECG-FM
Generate manifests → configure YAML → pick pretrained checkpoint → launch HiPerGator SLURM job (`sbatch train_ecgfm.slurm`) → monitor → export best validation checkpoint.

## Retraining the U-Net
Prepare LUDB/QTDB annotations → convert peaks to Gaussian labels → train (`python train_unet.py --config configs/unet.yaml`) → tune post-processing spacing → validate MAE/tolerance.

## Extending saliency
Candidate methods: Grad-CAM variants, integrated gradients, attention rollout, perturbation sensitivity. All must return output compatible with the unified visualization API.

## Testing levels
Unit (AUROC/F1, MAE, peak consistency, window merge) → integration (module-to-module, parallel branches, metadata) → system (held-out recordings, timing, memory) → acceptance (T-01…T-10).

## Reused vs. created
**Reused:** fairseq-signals, PyTorch, WFDB, SciPy/NumPy, HiPerGator SLURM templates.
**Created:** preprocessing pipeline, 12-channel wrapper, window segmentation, custom 1-D U-Net, Gaussian label generator, peak extraction/post-processing, multi-label inference with thresholds, heart-rate module, saliency module, unified inference API, evaluation scripts, visualization tools, Docker deployment scripts.

## Regulatory & legal
Likely SaMD (FDA Class II, 510(k)/De Novo). Align with IEC 62304, ISO 14971, HIPAA, cybersecurity guidance. Honor PhysioNet data-use agreements and review open-source licences before commercial release.
