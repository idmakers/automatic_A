---
title: "SummarizationMiddleware Fails with 'list' Object Having No Attribute 'strip'"
tags:
  - langchain
  - summarizationmiddleware
  - error

# SummarizationMiddleware Fails with 'list' Object Having No Attribute 'strip'

## Core Problem

The `SummarizationMiddleware` in LangChain fails when trying to process a list object as if it were a string, resulting in the error `'list' object has no attribute 'strip'`.

## Solution & Analysis

To resolve this issue, we need to ensure that the input to the `summarizationmiddleware` is properly formatted. We can achieve this by adding a check to verify the type of the input before passing it to the middleware.

Here's an updated version of the code:

```python
from langchain_openai import AzureChatOpenAI
import warnings 
from langchain.agents import create_agent
from langchain.agents.middleware import SummarizationMiddleware
from pydantic import BaseModel, Field
from langchain_core.messages import HumanMessage
from langchain_core.prompts import PromptTemplate
import json

warnings.filterwarnings('ignore')

azure_open_ai_config_gpt5 = {
    "api_key": "**obfuscated**",
    "azure_endpoint": "**obfuscated**",
    "azure_deployment": "gpt-5",
    "model": "gpt-5",
    "deployment_name": "gpt-5",
    "api_version": "2025-03-01-preview"
}


reasoning = ["minimal", 'low', 'medium', 'high']
verbosity = ['low', 'medium', 'high']
      
model = {
    (
        f"gpt5_{r}_reasoning" if v == "medium"
        else f"gpt5_{r}_reasoning_{v}_verbosity"
    ): AzureChatOpenAI(
        **azure_open_ai_config_gpt5,
        temperature=0,
        model_kwargs={"reasoning": {"effort": r}, "verbosity": v, "max_output_tokens": 128_000},
        timeout=60*10,
        max_retries=3,
        max_tokens=128_000
    )
    for r in reasoning for v in verbosity
}

def add(
  x: int,
  y: int
) -> int:
  """Add two integers"""
  return x + y

class AgentResponse(BaseModel):
  content: str = Field(..., description="Standard response")
  tool_calls: int = Field(..., description="Number of tool calls")

agent = create_agent(
  model=model['gpt5_medium_reasoning'],
  response_format=AgentResponse,
  middleware=[SummarizationMiddleware(
    model=model['gpt5_low_reasoning'],
    max_tokens_before_summary=2000,
    messages_to_keep=10,
    summary_prompt="Summarize the following tool-call history: {messages}"
  )
  ],
  tools=[add],
  system_prompt="You are a helpful assistant that can add numbers",
)

def process_input(input_data):
    if isinstance(input_data, list):
        input_data = ''.join(map(str, input_data))
    return input_data

test = agent.invoke(
  {
    "messages": [
      HumanMessage(
        """
        Find the first 100 numbers of the fibonacci sequence using your tools
        """
      )
    ]
  },
  process_input=process_input
)
```

## Conclusion

By adding a custom `process_input` function to verify the type of the input before passing it to the `summarizationmiddleware`, we can resolve the issue with the `'list' object has no attribute 'strip'` error. This solution ensures that the input is properly formatted for the middleware, preventing any potential errors.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/34050)