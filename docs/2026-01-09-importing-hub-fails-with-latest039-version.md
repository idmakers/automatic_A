---
title: "Importing Hub Fails with Latest langchain Version 0.3.9"
tags:
  - langchain
  - hub
  - import error

---

# Importing Hub Fails with Latest langchain Version 0.3.9

## Core Problem

The latest version of langchain (0.3.9) causes an issue when trying to import the hub module. The problem is specific to this version and has not been resolved by updating to a more recent stable version.

## Solution & Analysis

### Problem Reproduction Code

```python
from langchain import hub
```

### Error Message and Stack Trace

```
Traceback (most recent call last):
  File "/path/scripts/script.py", line 5, in <module>
    from langchain import hub
  File "/path/.venv/lib/python3.11/site-packages/langchain/__init__.py", line 8, in <module>
    from langchain.agents import MRKLChain, ReActChain, SelfAskWithSearchChain
  File "/path/.venv/lib/python3.11/site-packages/langchain/agents/__init__.py", line 2, in <module>
    from langchain.agents.agent import Agent
  File "/path/.venv/lib/python3.11/site-packages/langchain/agents/agent.py", line 10, in <module>
    from langchain.chains.base import Chain
  File "/path/.venv/lib/python3.11/site-packages/langchain/chains/__init__.py", line 2, in <module>
    from langchain.chains.conversation.base import ConversationChain
  File "/path/.venv/lib/python3.11/site-packages/langchain/chains/conversation/base.py", line 7, in <module>
    from langchain.chains.conversation.memory import ConversationBufferMemory
  File "/path/.venv/lib/python3.11/site-packages/langchain/chains/conversation/memory.py", line 7, in <module>
    from langchain.chains.conversation.prompt import SUMMARY_PROMPT
  File "/path/.venv/lib/python3.11/site-packages/langchain/chains/conversation/prompt.py", line 2, in <module>
    from langchain.prompts.prompt import PromptTemplate
  File "/path/.venv/lib/python3.11/site-packages/langchain/prompts/__init__.py", line 2, in <module>
    from langchain.prompts.base import BasePromptTemplate
  File "/path/.venv/lib/python3.11/site-packages/langchain/prompts/base.py", line 35, in <module>
    class BasePromptTemplate(BaseModel, ABC):
  File "/path/.venv/lib/python3.11/site-packages/langchain/prompts/base.py", line 41, in BasePromptTemplate
    @root_validator()
     ^^^^^^^^^^^^^^^^
  File "/path/.venv/lib/python3.11/site-packages/pydantic/deprecated/class_validators.py", line 240, in root_validator
    raise PydanticUserError(
pydantic.errors.PydanticUserError: If you use `@root_validator` with pre=False (the default) you MUST specify `skip_on_failure=True`. Note that `@root_validator` is deprecated and should be replaced with `@model_validator`.

For further information visit https://errors.pydantic.dev/2.10/u/root-validator-pre-skip
```

### Solution

As suggested in the top solution, try installing langchain-community package using pip:

```bash
pip install langchain_community
```

Alternatively, you can also consider updating to a more recent stable version of langchain.

## Conclusion

Importing the hub module with the latest langchain version (0.3.9) causes an issue due to a deprecated class validation error. Installing langchain-community package or updating to a more recent stable version might resolve this problem.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/28409)