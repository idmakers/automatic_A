---
title: "Error from using ChromaDB - ValueError: Could not connect to tenant default_tenant. Are you sure it exists?"
tags:
  - langchain
  - chromadb
  - error

---

# [Error from using ChromaDB]

## Core Problem
When using the ChromaDB library in LangChain, an unexpected `ValueError` is raised with the message "Could not connect to tenant default_tenant. Are you sure it exists?"

## Solution & Analysis

To resolve this issue, we need to ensure that the ChromaDB tenant exists and is correctly configured.

```python
import os
from langchain.chains import create_history_aware_retriever, create_retrieval_chain
from langchain.chroma import Chroma
```

It appears that the problem lies in the `persist_directory` parameter. If this directory does not exist or is not accessible, the connection to ChromaDB fails.

```python
vector_store = Chroma(
    collection_name="example_collection",
    embedding_function=embeddings,
    persist_directory="./chroma_langchain_db"  # Where to save data locally, remove if not neccesary
)
```

To fix this issue:

1. Ensure that the `persist_directory` exists and is accessible.
2. Update the directory path or ensure it's correct.

```python
vector_store = Chroma(
    collection_name="example_collection",
    embedding_function=embeddings,
    persist_directory="/path/to/chroma_langchain_db"  # Corrected directory path
)
```

## Conclusion

By ensuring that the `persist_directory` exists and is correctly configured, we can resolve the `ValueError` caused by ChromaDB.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/26884)