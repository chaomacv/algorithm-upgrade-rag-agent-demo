# Architecture

The project has four layers:

1. Case extraction converts a synthetic engineer-agent conversation into a structured `EngineeringCase`.
2. RAG chunks cases into task, constraint, and solution knowledge, then indexes them.
3. The planner combines task, retrieval results, repository facts, and the Skill into an execution plan.
4. Restricted tools modify only the toy project and run validation.

The system is intentionally small so the complete workflow is visible in a short interview demo.

