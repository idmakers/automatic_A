---
title: "Can't Import HuggingFacePipeline in LangChain"
tags:
  - langchain-ai
  - huggingface-pipeline
  - import-error

---

# Can't Import HuggingFacePipeline in LangChain

## Core Problem
When trying to use the `HuggingFacePipeline` class from the `langchain.llms` package, users are encountering an `ImportError: cannot import name 'HuggingFacePipeline' from 'langchain.llms'`. This issue arises when the import statement is incorrect or when there's a version mismatch between LangChain and Python.

## Solution & Analysis
To resolve this issue, you need to specify the exact module (file) where the `HuggingFacePipeline` class is defined. In this case, it's located in the `huggingface_pipeline.py` file under the `langchain.llms` package. Therefore, you should import it as follows:

```python
from langchain.llms.huggingface_pipeline import HuggingFacePipeline
```

Additionally, ensure that you're using the latest version of LangChain (v0.0.268) and that your Python version is updated to 3.10.

To update LangChain using pip, run the following command:

```bash
pip install -U langchain
```

If you're using conda, create a new environment with Python 3.10 as follows:

```bash
conda create -n $YOUR_ENV_NAME python==3.10
conda activate $YOUR_ENV_NAME
```

## Conclusion
By following these steps and ensuring that your LangChain version is up-to-date, you should be able to import the `HuggingFacePipeline` class successfully.

<!-- ADSENSE_PLACEHOLDER -->

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/9502)