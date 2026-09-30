# PD–CFNet: A Contrastive-enabled Fusion Network for Parkinson’s Disease Detection Using IMU and Phenotypic Data

## Installation

Python 3.10 or newer is required. Run commands from the project root.

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

Training uses CUDA when available and otherwise runs on CPU.

## Data Preparation

Place the PADS data under `PADS/PD/` and `PADS/HC/`. Each group must include `movement/observation_*.json`, the referenced `movement/timeseries/` files, and `questionnaire/questionnaire_response_*.json`.

```bash
python preprocess.py --config config.yaml
```

Preprocessing applies 100 Hz resampling, level-1 db4 denoising, a zero-phase fourth-order 20 Hz Butterworth filter, and 1-second windows with 50% overlap. Augmentation uses scaling in [0.9, 1.1], rotations of ±15° per axis, and Gaussian noise with standard deviation 0.01.

**The processed data is expected to be organized as follows:**

```text
Processed_data/
├── Normal/
│   ├── PD/
│   │   └── <subject_id>_<task>_<wrist>.npy
│   └── HC/
│       └── <subject_id>_<task>_<wrist>.npy
├── Augmented/
│   ├── PD/
│   │   └── <subject_id>_<task>_<wrist>.npy
│   └── HC/
│       └── <subject_id>_<task>_<wrist>.npy
└── manifest.json
```

Each array has shape `(number_of_windows, 100, 6)`. The manifest stores subject-wise splits, questionnaire features, and preprocessing metadata. Skip preprocessing if this dataset is already prepared; existing data directories are not overwritten.

## Training

Set training parameters and the output directory in `config.yaml`, then run:

```bash
python train.py --config config.yaml
python evaluate.py results/PD_HC_nested
```

Each run uses one manually specified set of contrastive parameters. Inner folds determine training durations before both stages are retrained on the complete outer development set and evaluated on the outer test set.

To inspect inner validation scores without evaluating outer test folds:

```bash
python train.py --config config.yaml --inner-only
```

Use a separate `output.results_dir` for each run. Model checkpoints, predictions, and metrics are saved there; evaluation produces ROC, confusion matrix, training curves, and bootstrap confidence intervals.
