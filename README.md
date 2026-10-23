# KPHarness

**KPHarness: Co-Evolving Knowledge and Probing for Long-Horizon Gaming Harnesses**

KPHarness adapts the execution harness around a frozen vision-language-action
model. Recovery knowledge guides probing, probing supplies evidence for harness
revisions, and paired sandbox evaluations determine which revisions to retain.

- [Project website](https://chenzk202212.github.io/adaptive-gaming-harnesses/)
- [Code and setup instructions](code/README.md)
- 🤗 [SFT weights (step 16200)](https://huggingface.co/cccsds/openha-qwen3vl8b-coldstart-step16200)
- 🤗 [GRPO weights (step 400)](https://huggingface.co/cccsds/openha-qwen3vl8b-grpo-step400)
- 🤗 [Minecraft evaluation set (149 tasks)](https://huggingface.co/datasets/cccsds/openha-minecraft-eval-149)

## Repository contents

- `code/curriculum/`: harness search, knowledge/probing policies, evaluation,
  experiment configurations, tests, and supporting research utilities.
- `index.html` and `assets/`: the project website, figures, and videos.

## Quick start

Use Linux and Python 3.10 or newer (the shared-file locks use POSIX `fcntl`).
From a clone of this repository, install the core
dependencies into your own virtual environment, then run the local stub test:

```bash
cd code
python -m pip install -r requirements.txt
python -m curriculum.kpharness_search --smoke
```

The stub exercises harness logic without a Minecraft server, API credentials,
or model downloads. It does not reproduce the Minecraft results. Live evaluation
also needs the compatible external ARES/MineStudio adapter, sandbox service,
checkpoint, and task files described in [the code guide](code/README.md).

Model weights and task definitions are hosted separately on Hugging Face.
Generated outputs, caches, local credentials, and downloaded checkpoints should
not be committed to this repository.
