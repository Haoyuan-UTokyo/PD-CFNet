# PD–CFNet: A Contrastive-enabled Fusion Network for Parkinson’s Disease Detection Using IMU and Phenotypic Data

## Code coming soon!

## Installation

```bash
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements.txt
```

## Data Preparation

Download the [PADS dataset from PhysioNet](https://physionet.org/content/parkinsons-disease-smartwatch/1.0.0/).

Place the PADS data under `PADS/PD/` and `PADS/HC/`. Each group includes `movement/observation_*.json`, the referenced `movement/timeseries/` files, and `questionnaire/questionnaire_response_*.json`.

```bash
python preprocess.py --config config.yaml
```

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

## Training

Set training parameters in `config.yaml`, then run:

```bash
python train.py --config config.yaml
python evaluate.py results/PD_HC_nested
```

To inspect inner validation scores without evaluating outer test folds:

```bash
python train.py --config config.yaml --inner-only
```

Use a separate `output.results_dir` for each run. Training saves model checkpoints, predictions, and metrics there. Evaluation saves only `evaluation_summary.json`, containing per-fold and pooled metrics, fold means/standard deviations, and 95% bootstrap confidence intervals.
