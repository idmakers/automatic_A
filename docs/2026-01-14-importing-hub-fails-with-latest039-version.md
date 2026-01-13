---
title: "Importing Hub Fails with Latest LangChain Version 0.3.9"
tags:
  - langchain
  - hub
  - import error

# Core Problem
The latest version of LangChain, specifically 0.3.9, causes an issue when trying to import the `hub` module.

## Solution & Analysis

### Step 1: Check for deprecated imports

Due to a deprecation notice in Pydantic, the original code importing the hub module will not work with the latest version of LangChain. To fix this, we need to update our imports using the recommended `@model_validator` decorator instead of the deprecated `@root_validator`.

### Step 2: Update the import

```python
from langchain.agents.agent import Agent
from langchain.chains.conversation.base import ConversationChain
from langchain.prompts.prompt import PromptTemplate
from langchain.prompts.base import BasePromptTemplate
```

## Conclusion

In conclusion, upgrading to the latest version of LangChain (0.3.9) results in an `ImportError` due to a Pydantic deprecation issue. By updating our imports to use the recommended `@model_validator` decorator, we can resolve this issue and continue using the hub module for pulling prompts.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/28409)