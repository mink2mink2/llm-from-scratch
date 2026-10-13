# Study environment — 『밑바닥부터 만들면서 배우는 LLM』

Everything that matters lives in this repo (Gitea `admin/llm-study`): the book's code, your own code, and the
environment (`study-env/pyproject.toml` + `uv.lock`, Python 3.13, torch 2.11.0). A machine holds nothing you would lose.

Not in git: model weights and checkpoints (`*.pth`, `gpt2/`, …) — re-download or re-train on the new machine.
Notebook memory (variables) does not move either — re-run the cells.

## m2Msi (RTX 3060) — the container is already running

From any Mac (home or remote over meshnet), open a tunnel and the browser:

```bash
ssh -N -L 8888:127.0.0.1:8888 <your m2Msi ssh alias>      # leave it open
ssh <your m2Msi ssh alias> 'cat ~/.config/llm-study/jupyter.env'   # the token, read it on your own screen
open http://localhost:8888/lab                              # paste the token once
```

Commit from JupyterLab's terminal (File → New → Terminal), working dir `/work`:

```bash
git add -A && git commit -m "ch02: tokenizer exercises" && git push
```

Restart / rebuild: `cd ~/study/llm-from-scratch && docker compose -f study-env/compose.yaml up -d --build`.
The container comes back by itself after a reboot (`restart: unless-stopped`).

## Another CUDA PC (e.g. RTX 5070 Ti) — same container

Needs Docker with the NVIDIA runtime and driver >= 570 (torch is built for CUDA 12.8, which covers RTX 50xx).

```bash
git clone <gitea url>/admin/llm-study.git ~/study/llm-from-scratch && cd ~/study/llm-from-scratch
mkdir -p ~/.config/llm-study && echo "JUPYTER_TOKEN=$(openssl rand -hex 16)" > ~/.config/llm-study/jupyter.env
# ~/.config/llm-study/gitconfig and git-credentials: copy the layout from m2Msi (never paste tokens into chat)
docker compose -f study-env/compose.yaml up -d --build
```

## MacBook (M1) — no container (Docker on macOS cannot use the GPU)

```bash
git clone <gitea url>/admin/llm-study.git ~/study/llm-from-scratch && cd ~/study/llm-from-scratch
uv sync --project study-env          # same lock, macOS build of torch (MPS)
uv run --project study-env jupyter lab
```

## Check the GPU

```bash
python -c "import torch; print(torch.__version__, torch.cuda.is_available(), torch.backends.mps.is_available())"
```
