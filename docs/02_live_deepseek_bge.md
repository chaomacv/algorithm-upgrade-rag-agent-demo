# Live DeepSeek + BGE-M3 Demo

This mode keeps the same teaching scenario but replaces the mock components with real integrations:

- `BgeM3EmbeddingProvider` for semantic vectors.
- `LLMPlanner` with DeepSeek for planning.

Use this after the teaching demo is understood.

## Requirements

You need:

- Python environment with BGE-M3 dependencies installed.
- DeepSeek environment variables loaded from `load_deepseek_env.sh`.
- Network access to DeepSeek.
- Hugging Face model files available or downloadable.

The local `password.txt` file is intentionally not for GitHub commits. The helper script
`load_deepseek_env.sh` reads that file when you choose to use DeepSeek locally.

## Run It

```bash
cd algorithm-upgrade-rag-agent-demo
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
bash scripts/run_live_demo.sh
```

The script runs:

```bash
python -m pytest -q
python -m algo_rag_demo.cli build-index --provider bge-m3
python -m algo_rag_demo.cli demo-failure --provider bge-m3 --planner deepseek
```

## Important Detail

The vector index must match the embedding provider.

If `outputs/index/cases.index` was built with `mock`, it is 64-dimensional. BGE-M3 queries are 1024-dimensional. Running BGE-M3 retrieval against a mock index will fail with a FAISS dimension assertion.

That is why the live script always rebuilds the index with:

```bash
python -m algo_rag_demo.cli build-index --provider bge-m3
```

## What Can Vary

This mode is more realistic but less deterministic:

- DeepSeek may phrase plans differently.
- Model loading may take longer on first run.
- Network and API availability matter.
- BGE-M3 retrieval scores differ from mock retrieval scores.

`PlanExecutor` keeps execution stable by treating LLM output as intent and mapping edits to safe templates.

## Expected Outcome

A successful run should end with:

```text
Final Result
SUCCESS
```

The final report should show:

```json
{
  "status": "success",
  "validation": {
    "build": "passed",
    "interface": "passed",
    "runtime": "passed",
    "algorithm_switch": "passed",
    "rollback": "passed"
  }
}
```

Depending on DeepSeek wording, extra validation keys such as `residual_map`, `tests`, or `rollback_readiness` may also appear. The executor maps these to the closest supported validation.
