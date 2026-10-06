# Demo Walkthrough

Run:

```bash
python -m algo_rag_demo.cli build-index
python -m algo_rag_demo.cli demo-failure
```

The failure demo:

1. Performs a direct replacement.
2. Fails interface validation because `residual_map` is missing.
3. Searches historical cases using the concrete failure.
4. Retrieves `CASE_DRE_001_solution`.
5. Adds the compatibility adapter.
6. Switches configuration to `new_llf`.
7. Verifies interface, build, runtime, algorithm switch, and rollback.

