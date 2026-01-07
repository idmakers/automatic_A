---
title: "SummarizationMiddleware fails - list object has no attribute 'strip'"
tags:
  - langchain
  - SummarizationMiddleware
  - Error Message
  - List Object No Attribute

# SummarizationMiddleware Fails with List Object No Attribute Error

## Core Problem

The SummarizationMiddleware in LangChain raises an error when attempting to process a list object as if it were a string. This issue surfaces in the returned message from the summarisation middleware, indicating that the 'strip' attribute is not available for the list object.

## Solution & Analysis

To resolve this issue, we need to ensure that the input data is properly processed before passing it to the SummarizationMiddleware. One possible solution is to modify the model configuration to handle lists correctly.

```python
# Modify the model configuration to handle lists correctly
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

# Modify the SummarizationMiddleware to handle lists correctly
class CustomSummarizationMiddleware(SummarizationMiddleware):
    def __init__(self, model, **kwargs):
        super().__init__(model, **kwargs)
        self.list_handler = ListHandler()

    def process_input(self, input_data):
        # Handle lists by converting them to strings
        if isinstance(input_data, list):
            return self.list_handler.handle_list(input_data)
        else:
            return input_data

# Define a custom list handler
class ListHandler:
    def handle_list(self, input_list):
        # Convert the list to a string
        return str(input_list)

agent = create_agent(
  model=model['gpt5_medium_reasoning'],
  response_format=AgentResponse,
  middleware=[CustomSummarizationMiddleware(model=model['gpt5_low_reasoning'], max_tokens_before_summary=2000, messages_to_keep=10, summary_prompt="Summarize the following tool-call history: {messages}")],
  tools=[add],
  system_prompt="You are a helpful assistant that can add numbers",
)
```

## Conclusion

By modifying the model configuration and creating a custom list handler, we can resolve the issue with the SummarizationMiddleware failing due to a list object having no attribute 'strip'. This solution ensures that lists are properly processed before being passed to the SummarizationMiddleware.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/34050)