# RAG Pipeline

Cases are chunked into:

- task
- constraint
- solution

Each chunk preserves `chunk_id`, `case_id`, `chunk_type`, `module`, `final_status`, and `source`.

The index uses normalized vectors and inner product similarity. `faiss-cpu` is used when installed; otherwise the demo stores vectors in a NumPy-backed `cases.index` file so the offline demo still runs.

