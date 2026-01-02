---
title: "Resolving 'Session not found: default' Error when Using InMemoryRunner with LLMAgent in Google ADK"
tags: debugging, google adk, llm agent, inmemoryrunner, sessionservice
---

## Core Problem

When using the Google ADK's LLM Agent (<code>gemini-2.5-flash-lite</code>) with <code>InMemoryRunner</code> and <code>InMemorySessionService</code>, an error is encountered that prevents the session from being recognized: "Session not found: default".

## Solution & Analysis

The issue lies in how <code>InMemoryRunner</code> and <code>InMemorySessionService</code> interact. The problem arises because the session ID passed to <code>runner.run_async</code> does not match the one stored by the <code>InMemorySessionService</code>. This discrepancy leads to the "Session not found" error.

To resolve this, ensure that the session ID provided to both the runner and the session service is consistent. One approach is to use a unique session ID for each agent instance or modify the helper function to reuse an existing session if available.


```python
import asyncio
from google.adk.agents.llm_agent import Agent
from google.adk.runners import InMemoryRunner
from google.adk.sessions import InMemorySessionService
from google.genai.types import Content, Part

_APP_USER_ID = "system"
_APP_SESSION_ID = "existing_session_id"

_SESSION_SERVICE = InMemorySessionService()

async def _run_agent(agent: Agent, prompt: str) -> str:
    # Attempt to create a session
    existing_session = await _SESSION_SERVICE.get_session(
        app_name="duck_pa",
        user_id=_APP_USER_ID,
        session_id=_APP_SESSION_ID
    )

    if not existing_session:
        # Create a new session with the provided ID
        existing_session = await _SESSION_SERVICE.create_session(
            app_name="duck_pa",
            user_id=_APP_USER_ID,
            session_id=_APP_SESSION_ID
        )

    runner = InMemoryRunner(agent)
    input_content = Content(parts=[Part(text=prompt)])

    async for event in runner.run_async(
        new_message=input_content,
        user_id=existing_session.user_id,
        session_id=existing_session.id
    ):
        if event.is_final_response():
            return &quot;&quot;.join(
                part.text for part in event.content.parts if getattr(part, &quot;text&quot;, None)
            )

    raise RuntimeError(&quot;Agent did not return a final response&quot;}
```

## Conclusion

By modifying the helper function to retrieve an existing session before creating a new one and using its ID when running the agent, the "Session not found: default" error can be resolved. This approach ensures that both the runner and the session service use the same session ID, thereby resolving the issue with InMemoryRunner and LLMAgent in Google ADK.