---
title: "Error from using ChromaDB - ValueError: Could not connect to tenant default_tenant. Are you sure it exists?"
tags:
  - langchain
  - chromadb
  - error
---

# Using ChromaDB with LangChain: Error Connection to Tenant Default Tenant

## Core Problem
When attempting to use the `Chroma` vector store from the `langchain` library, users may encounter a `ValueError` exception with the message "Could not connect to tenant default_tenant. Are you sure it exists?"

## Solution & Analysis

To resolve this issue, ensure that the ChromaDB tenant exists and is properly configured.

### Configuring ChromaDB Tenant

By default, ChromaDB uses a tenant named `default_tenant`. However, if this tenant does not exist in your environment, you will receive the aforementioned error.

#### Solution

1.  Check if the ChromaDB tenant exists by visiting the [ChromaDB documentation](https://python.langchain.com/v0.2/docs/integrations/vectorstores/chroma/).
2.  Create a new tenant or use an existing one that matches your environment.
3.  Update your `Chroma` initialization to use the correct tenant name.

### Example Code

```python
vector_store = Chroma(
    collection_name="example_collection",
    embedding_function=embeddings,
    persist_directory="./chroma_langchain_db",  # Where to save data locally, remove if not neccesary
    tenant_name="your_tenant_name"  # Use the correct tenant name
)
```

#### Alternative Solution

If you are using a local ChromaDB instance, you can create a new tenant manually by running the following command in your terminal:

```bash
chromadb tenant add --name "your_tenant_name"
```

Replace `"your_tenant_name"` with the desired name for your ChromaDB tenant.

## Conclusion

In conclusion, to resolve the `ValueError` exception when using ChromaDB with LangChain, ensure that the ChromaDB tenant exists and is properly configured. By following these steps, you can successfully integrate ChromaDB into your application and leverage its vector store capabilities.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/26884)