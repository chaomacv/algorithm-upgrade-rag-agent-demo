# Architecture For Reuse

This project has two audiences:

1. Readers who want a quick teaching demo.
2. Readers who want reusable code for their own RAG-assisted engineering workflow.

The reusable parts are separated from the teaching scenario.

## Layers

```text
examples/data/raw
-> case_pipeline
-> examples/data/cases
-> chunker
-> examples/data/knowledge/chunks.json
-> embedding
-> index
-> retriever
-> planner
-> executor
-> tool registry
-> runs
```

## Teaching-Specific Layer

`algo_rag_demo/demo_scenarios/dre_failure_recovery.py` owns the fixed storyline:

- the simulated user task,
- the intentionally unsafe direct replacement,
- the error query,
- the two-phase run artifacts,
- the final report.

This file is for explanation and demos. It is not the part to copy first when adapting the project.

## Reusable Components

`algo_rag_demo/case_pipeline/`

Converts raw conversation logs into structured `EngineeringCase` objects and schema-aligned chunks.

`algo_rag_demo/rag/`

Builds embeddings, saves the vector index, and retrieves top-k chunks for a task or error query.

`algo_rag_demo/agent/planner.py`

Defines planner implementations:

- `MockPlanner`: deterministic, offline planner.
- `LLMPlanner`: provider-neutral planner that can call DeepSeek.

Planner output includes human-readable fields and an `executable_plan.steps` list.

`algo_rag_demo/agent/executor.py`

Defines `PlanExecutor`, which executes structured steps through safe tools. It supports:

- `inspect`
- `search`
- `patch`
- `backup` / `checkpoint`
- `validate`

The executor does not let the LLM write arbitrary files. Patch steps are interpreted as intent and mapped to known safe edit templates.

`algo_rag_demo/agent/tool_registry.py`

Provides restricted tools:

- file reading and writing inside `demo_project`,
- code search,
- build check,
- interface check,
- runtime check,
- algorithm switch check,
- checkpoint,
- rollback.

This is the main boundary between LLM planning and actual system mutation.

## Adapting To Another Project

Start by changing these pieces:

1. Replace `demo_project/` with your target toy or real project.
2. Change `ToolRegistry` validation methods to match your test commands.
3. Change `PlanExecutor._apply_safe_patch()` so patch intents map to your safe edit operations.
4. Add your own raw conversations under `examples/data/raw/`.
5. Extract structured cases into `examples/data/cases/`.
6. Rebuild the index.

Keep the scenario layer separate. For a new demo, create another module under:

```text
algo_rag_demo/demo_scenarios/
```

For production-like usage, call the reusable components directly instead of relying on the fixed DRE teaching scenario.

## Why The Separation Matters

The scenario needs a narrative:

```text
first attempt fails
-> retrieve error
-> repair succeeds
```

Reusable code needs stable boundaries:

```text
planner produces steps
executor interprets steps
tool registry enforces safety
validation decides success
```

Keeping those concerns separate makes the project easier to explain and easier to adapt.
