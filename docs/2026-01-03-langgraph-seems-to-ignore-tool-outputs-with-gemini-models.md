---
title: "LangGraph Ignores Tool Outputs with Gemini Models"
tags:
  - langgraph
  - gemini
  - llm
  - tool outputs
---

# LangGraph Ignores Tool Outputs with Gemini Models

## Core Problem

The issue reported by the user is that when using a Gemini model with LangGraph, the LLM correctly invokes a tool but appears unable to interpret or process the tool's result. This problem persists even though the agent properly reads and identifies the target company in the document.

## Solution & Analysis

To resolve this issue, we need to analyze the code and understand why the LangGraph is not processing the tool output. The provided code uses the `ToolNode` class from LangGraph to add tools to the agent graph. However, it seems that there might be an issue with how the tool outputs are being handled.

One possible solution could be to modify the `process_node` function to handle the tool output correctly. We can do this by adding a condition to check if the `tool_calls` attribute of the last message is not empty and then iterating over each call to extract the result. Here's an updated version of the `process_node` function:

```python
async def process_node(state: AgentState) -> AgentState:
    # ... (rest of the code remains the same)
    
    if state.get("messages", []) and hasattr(state["messages"][-1], "tool_calls"):
        tool_calls = state["messages"][-1].tool_calls
        for tc in tool_calls:
            print(f"      - {tc['name']}: {tc['args']}")
            
            # Extract the result from the tool output
            result = tc.get("result")
            if result is not None:
                # Process the result here
                process_result(result)
    
    return {"messages": [state["messages"]]}
```

In this updated code, we're assuming that each tool call returns a `result` attribute. We can modify this to match the actual format of the tool output.

## Conclusion

By modifying the `process_node` function to handle the tool output correctly, we should be able to resolve the issue and get the LangGraph to process the tool outputs as expected.