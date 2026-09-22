# Working copy setup

This repository is RuiyuM's fork of [yunbeizhang/BETA](https://github.com/yunbeizhang/BETA).
The original authorship, history, and LICENSE are retained.

## Python environment

Use a separate environment; the commands below install the project's pinned
PyTorch release with CUDA 11.8 wheels (tested on an RTX A5000 with driver 535).
No system CUDA or driver changes are required.

```bash
conda create -n beta python=3.10 pip -y
conda env config vars set -n beta PYTHONNOUSERSITE=1
conda activate beta
python -m pip install torch==2.7.1 torchvision==0.22.1 torchaudio==2.7.1 \
  --index-url https://download.pytorch.org/whl/cu118
python -m pip install -r requirements.txt
python -m pip check
python main.py --help
```

The CUDA wheel versions follow the [official PyTorch installation instructions](https://pytorch.org/get-started/previous-versions/).
The original `environment.yaml` is an upstream environment snapshot; use the
commands above for this setup.
`PYTHONNOUSERSITE=1` keeps unrelated packages in `~/.local` out of this environment.

## Run

```bash
conda activate beta
export CUDA_VISIBLE_DEVICES=0
export DATA_DIR=/path/to/datasets
bash main.sh
```

`DATA_DIR` must contain `ImageNet/` and `ImageNet-C/`, as described in README.md.
Pretrained weights are downloaded on first use. A successful environment check
does not validate dataset availability or reproduce the reported accuracy.

## Git remotes

- `origin`: `RuiyuM/BETA` (your fork)
- `upstream`: `yunbeizhang/BETA` (original project)

Push your changes to `origin`. Fetch `upstream` when you want to inspect new
changes from the original authors. Server push access uses a repository-specific
SSH deploy key; no password or GitHub token is stored in this repository.

## Validation (2026-09-22)

- Python 3.10.21, PyTorch 2.7.1+cu118, torchvision 0.22.1+cu118.
- `pip check` and `python main.py --help` passed.
- RTX A5000: ViT-B/16 target and ViT-S/16 helper completed a BETA forward and
  backward step with synthetic images and random weights. Outputs were finite,
  prompt parameters updated, and reset restored their initial values.
- ImageNet / ImageNet-C were not found in the user's inspected data directories;
  dataset evaluation and pretrained weight downloads have not been validated.
