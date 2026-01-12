---
title: "Error from using ChromaDB - ValueError: Could not connect to tenant default_tenant. Are you sure it exists?"
tags:
  - langchain-chroma
  - error-handling
  - chromadb

---

# [Error with ChromaDB]

## Core Problem
When using the Chroma library in LangChain, a `ValueError` is raised due to an issue with connecting to the default tenant. This error occurs even though proper documentation has been followed.

## Solution & Analysis
To resolve this issue, ensure that the Chroma tenant exists and is correctly configured.

```python
vectorstore = Chroma.from_texts(to_vectorize, RagSamples.embedding_function, metadatas=examples)
```

In the provided code snippet, a `Chroma` object is created without specifying a valid tenant. This might be causing the error.

To fix this issue:

1.  Check if the default tenant exists by accessing your Chroma instance: `vectorstore.get_tenant('default_tenant')`
2.  If the tenant does not exist, create it using the `Chroma.create_tenant` method.
3.  Ensure that the Chroma configuration is correct and properly set up.

Here's an example of how to correctly configure the Chroma library:

```python
vectorstore = Chroma(
    collection_name="example_collection",
    embedding_function=embeddings,
    persist_directory="./chroma_langchain_db",  
)
```

In this corrected version, a valid tenant is specified, and the `Chroma` object is created with a correct configuration.

## Conclusion
By following these steps and ensuring that the Chroma library is correctly configured, you should be able to resolve the error and successfully use the ChromaDB.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/26884)