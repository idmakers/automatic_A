---

title: "Error from using ChromaDB - ValueError: Could not connect to tenant default_tenant. Are you sure it exists?"
tags:
  - langchain-chroma
  - error-message
  - chromadb

---

# [Use the Title]

## Core Problem
The user is experiencing an error when using ChromaDB, specifically a `ValueError` with the message "Could not connect to tenant default_tenant. Are you sure it exists?"

## Solution & Analysis
To resolve this issue, we need to ensure that the ChromaDB tenant exists and is properly configured.

Firstly, let's check if the tenant exists by looking at the configuration code:
```python
vector_store = Chroma(
    collection_name="example_collection",
    embedding_function=embeddings,
    persist_directory="./chroma_langchain_db",  # Where to save data locally, remove if not neccesary
)
```
In this case, the tenant name is `default_tenant`. We need to verify that this tenant exists in ChromaDB.

To do this, we can check the ChromaDB documentation or contact their support team. It's possible that the tenant was deleted or renamed accidentally.

Once we confirm that the tenant exists, we need to update the configuration code to use the correct tenant name:
```python
vector_store = Chroma(
    collection_name="example_collection",
    embedding_function=embeddings,
    persist_directory="./chroma_langchain_db",  # Where to save data locally, remove if not neccesary
    tenant="default_tenant"  # Update the tenant name
)
```
By making this change, we should be able to resolve the error and use ChromaDB successfully.

## Conclusion
To avoid this issue in the future, make sure to verify that your ChromaDB tenant exists and is properly configured. If you're unsure, consult their documentation or contact their support team for assistance.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/26884)