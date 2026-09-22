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
- The server now has the ImageNet-1K validation split under
  `data/ImageNet/val/` (the project's `data` directory links to the dataset
  storage location). Activating `beta` sets `DATA_DIR` automatically.
  Source: [ILSVRC/imagenet-1k](https://huggingface.co/datasets/ILSVRC/imagenet-1k),
  revision `49e2ee26f3810fb5a7536bbf732a7b07389a47b5`.
- ImageNet-C is separate and has not been downloaded. Use `--corruption original`
  for the clean validation set; the default `main.sh` targets ImageNet-C.
  Full accuracy evaluation and pretrained weight downloads have not been validated.

For clean ImageNet evaluation on the configured server:

```bash
conda activate beta
CUDA_VISIBLE_DEVICES=0 python main.py --corruption original \
  --data "$DATA_DIR/ImageNet" --algorithm beta --model vitb16 --local_helper vits16
```
