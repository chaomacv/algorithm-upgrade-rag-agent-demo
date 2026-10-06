# Teaching Demo

This is the lowest-friction path through the project. It is designed for explaining the whole RAG + Planner + Executor loop without requiring an API key, network access, or a model download.

## Run It

```bash
cd algorithm-upgrade-rag-agent-demo
python -m venv .venv
source .venv/bin/activate
pip install -r requirements-lite.txt
bash scripts/run_teaching_demo.sh
```

The script uses:

- `MockEmbeddingProvider`
- `MockPlanner`
- `PlanExecutor`

That makes the demo deterministic and suitable for walkthroughs.

## What It Demonstrates

```text
historical cases
-> schema-aligned chunks
-> mock vector index
-> task retrieval
-> first failed execution
-> error-specific retrieval
-> repair plan
-> controlled executor
-> validation
-> final report
```

The teaching scenario is intentionally fixed. The simulated user task is:

```text
Replace OldDRE with NewLLF while keeping downstream interface compatible
```

The first execution intentionally performs a direct replacement. That fails because `NewLLF` does not return `residual_map`, while downstream code still expects it.

The second retrieval uses the concrete error:

```text
DRE interface validation failed 'residual_map' residual_map NewLLF
```

That retrieves the historical case explaining that a compatibility adapter is required.

## Where The Code Lives

- `scripts/run_teaching_demo.sh`: one-command teaching entry point.
- `algo_rag_demo/demo_scenarios/dre_failure_recovery.py`: the fixed teaching storyline.
- `algo_rag_demo/agent/executor.py`: reusable controlled executor.
- `algo_rag_demo/agent/tool_registry.py`: safe tools, validation, checkpoint, and rollback.
- `algo_rag_demo/rag/retriever.py`: chunk retrieval.

## What To Show

After running the script, open the newest directory under `outputs/runs/`. The most useful artifacts are:

- `initial_retrieval.json`: retrieval from the task text.
- `initial_execution.json`: the intentional `residual_map` failure.
- `retrieval.json`: retrieval from the failure text.
- `plan.json`: repair plan.
- `repair_execution.json`: execution steps and tool calls.
- `final_report.json`: compact success/failure summary.

This path is best for first-time readers because it explains the loop without DeepSeek or BGE-M3 setup.
