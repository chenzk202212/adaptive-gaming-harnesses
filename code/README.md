# KPHarness code

Run the commands below from this `code/` directory so that the `curriculum`
package and configuration paths resolve correctly. Linux and Python 3.10+ are
required; shared-file locking uses POSIX `fcntl`, unavailable in native Windows.

## Local setup and tests

In a virtual environment:

```bash
python -m pip install -r requirements-test.txt
python -m curriculum.kpharness_search --smoke
python -m curriculum.test_kpharness
python -m curriculum.test_kpharness_event
python -m curriculum.test_harness_skill_bank
python -m pytest curriculum/test_contextual_probe_bandit.py -q
```

These tests use the local stub, not a live Minecraft environment. Test artifacts
are written under `curriculum/outputs/` and are ignored by Git. The standalone
test modules reset their own output subdirectories at the start of each run.

## Main entry points

- `kpharness_search.py`: paired harness proposal and validation.
- `harness_runtime.py`, `harness_schema.py`: execution and harness configuration.
- `contextual_probe_bandit.py`, `knowledge_prober.py`: probe selection.
- `harness_skill_bank.py`, `harness_success_memory.py`: reusable recovery knowledge.
- `batch_openha_ablation.py`: bare actor and fixed-harness evaluation.
- `run_kpharness_queue.py`: sequential harness evolution across tasks.
- `eval_frozen_harness_transfer.py`,
  `eval_fixed_knowledge_probe_generalization.py`: frozen-transfer evaluations.
- `configs/`: stub, live-environment, and ablation configuration examples.

Supporting world-model, visualization, and replay utilities are retained because
they share code with experiment workflows. Real world-model backends require
external `train.*` modules and checkpoints that are not included here.

## Weights and evaluation tasks

- [SFT step 16200](https://huggingface.co/cccsds/openha-qwen3vl8b-coldstart-step16200)
- [GRPO step 400](https://huggingface.co/cccsds/openha-qwen3vl8b-grpo-step400)
- [149-task evaluation split](https://huggingface.co/datasets/cccsds/openha-minecraft-eval-149)

Download a checkpoint to a local directory before using the HF VLA backend;
`qwen_vla.py` expects a local directory, not a Hub repository ID. Install
`transformers` with Qwen3-VL support and `accelerate` for that backend.

The released evaluation set provides task definitions (50 mining, 49 combat,
50 crafting). Batch/queue workflows also expect a local `manifest.json` with a
`tasks` list, whose records contain `file` (relative to the task directory),
`index`, `task_name`, and `task_type`. Supply this manifest for the task selection
you intend to evaluate. Historical transfer scripts additionally require prior
run outputs; those outputs are not bundled with the source release.

## Live environment configuration

Live Minecraft execution requires the external adapter imported as
`ares.mm_agent.envs_and_tools.openworld.minestudio_env`, a compatible sandbox
endpoint, and its credentials. This adapter/service is not included in the
archive. Installing the core requirements alone does not provide it.

Set the following environment variables as appropriate:

- `MINESTUDIO_ENDPOINT`, `MINESTUDIO_TOKEN`: sandbox service and credentials.
- `LLM_API_REQUEST_URL`, `LLM_API_APP_ID`: OpenAI-compatible proposer gateway.
  `GEMINI_API_KEY` or `GOOGLE_API_KEY` can supply its credential instead; configure
  a compatible chat-completions endpoint as well.
- `VLA_CHECKPOINT`: local checkpoint directory for the default VLA and batch runs.
- `OPENHA_TASK_DIR`, `OPENHA_MANIFEST`: local task folder and batch manifest.
- `OPENHA_EVAL_ROOT`: evaluation root for historical-rescue manifest construction.
- `OPENHA_ALL_TASK_DIR`: larger OpenHA task pool for transfer evaluation.

For YAML-driven runs, edit `vla_model_path`, `task_config`, and any service/path
fields in the selected configuration. Explicit YAML fields take precedence over
module defaults. `/path/to/...` and `*.example.com` values are placeholders.
Keep credentials in the environment rather than configuration files.

```bash
python -m curriculum.kpharness_search --config curriculum/configs/kpharness.yaml
```

For a live example, adapt `configs/kpharness_minestudio_hf_rl.yaml` to your
environment. GPU inference, sandbox/API access, and the full paper experiments
are separate from the local stub checks above.

Two fixed-task GPU launcher wrappers were omitted from this release:
`run_k1_t0_smoke.py` and `run_k1_t0_compare3.py`. Their task configurations remain
available and can be passed directly to `curriculum.kpharness_search --config`.
