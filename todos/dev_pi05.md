# Pi 0.5

[github](https://github.com/Physical-Intelligence/openpi)

## Installation
install uv from [uv installation](https://docs.astral.sh/uv/getting-started/installation/)

```
git clone --recurse-submodules git@github.com:Physical-Intelligence/openpi.git

# Or if you already cloned the repo:
git submodule update --init --recursive

GIT_LFS_SKIP_SMUDGE=1 uv sync
GIT_LFS_SKIP_SMUDGE=1 uv pip install -e .

```

> Problems
> 1. del uv.lock
> 2. install `chex` by `pip install chex==0.1.89`
> 3. inference is performed on a batch data, e.g. [10, C, H, W]. The results are computed to predict 10 actions. Hence the inference time should divide by 10.
