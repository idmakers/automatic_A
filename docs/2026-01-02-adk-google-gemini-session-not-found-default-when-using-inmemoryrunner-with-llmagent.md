---
title: "Resolving 'Session not found: default' when Using InMemoryRunner with LlmAgent"
tags:
  - google-adk
  - llm-agent
  - inmemoryrunner
  - inmemoriesessionservice

---

# Resolving 'Session not found: default' when Using InMemoryRunner with LlmAgent

## Core Problem
When using the Google ADK LLM agent (`gemini-2.5-flash-lite`) in a Flask project to check tests and review test content, an error is encountered while running the agent with `InMemoryRunner` and `InMemorySessionService`. The specific error message received is "Session not found: default."

## Solution & Analysis
To resolve this issue, it's essential to understand how `InMemoryRunner` and `InMemorySessionService` interact in the current ADK version (1.21.0). By analyzing the code and the behavior of these classes, we can identify the root cause of the problem.

The problem arises from the fact that `InMemorySessionService` is used to create a session with a specific `session_id`. However, when passing this session to `InMemoryRunner`, it expects a different format. To fix this issue, you need to modify the way you're creating and passing the session.

Here's an updated version of your `_run_agent` function that correctly handles the session:

```python
async def _run_agent(agent: Agent, prompt: str) -> str:
    # Attempt to create a session
    session = await SESSION_SERVICE.create_session(
        app_name="duck_pa",
        user_id=_APP_USER_ID,
        session_id=_APP_SESSION_ID
    )

    runner = InMemoryRunner(agent)
    input_content = Content(parts=[Part(text=prompt)])

    async for event in runner.run_async(
        new_message=input_content,
        user_id=session.user_id,
        session_id=session.session_id  # Update the session_id here
    ):
        if event.is_final_response():
            return "".join(part.text for part in event.content.parts if getattr(part, "text", None))

    raise RuntimeError("Agent did not return a final response")
```

In this updated code, we're passing `session.session_id` instead of `session.id`. Note that `session.id` might be different from the session ID used when creating it.

## Conclusion
By modifying the way you create and pass the session to `InMemoryRunner`, you can resolve the "Session not found: default" error. This solution should work with the current ADK version (1.21.0) and `InMemoryRunner` and `InMemorySessionService`.