---
title: "Jinja2 loop.index0 blocked by RestrictedSandboxedEnvironment in LangChain"
tags:
  - langchain-ai
  - jinja2
  - templating

---

# Jinja2 Loop Index0 Blocked by RestrictedSandboxedEnvironment in LangChain

## Core Problem

When using `ChatPromptTemplate` with `template_format="jinja2"`, a simple Jinja2 template that uses the built-in `loop.index0` works correctly with plain Jinja2, but fails with a `jinja2.exceptions.SecurityError` in LangChain.

## Solution & Analysis

```python
from langchain_core.prompts.chat import ChatPromptTemplate

prompt = "{% for it in items %} {{ loop.index0 }}{% endfor %}"
items = [1, 2, 3]

message = ChatPromptTemplate.from_messages(
    messages=[("system", prompt)],
    template_format="jinja2",
).format_messages(
    items=items
)

print(message[0].content)
```

Error Message:
```shell
jinja2.exceptions.SecurityError: Access to attributes is not allowed in templates. Attempted to access 'index0' on LoopContext. Use only simple variable names like {{variable}} without dots or methods.
```

To resolve this issue, we can use a less restricted Jinja environment for trusted templates only.

```python
from langchain_core.prompts.chat import ChatPromptTemplate

# Create a template with a less restricted Jinja environment
template = "{% for it in items %} {{ loop.index0 }}{% endfor %}"

prompt = ("system", template)
items = [1, 2, 3]

message = ChatPromptTemplate.from_messages(
    messages=prompt,
    template_format="jinja2",
).format_messages(items=items)

print(message[0].content)
```

Alternatively, we can use an explicitly "unsafe / trusted" mode for applications that fully control the template strings.

```python
from langchain_core.prompts.chat import ChatPromptTemplate

# Create a template with an explicitly "unsafe / trusted" mode
template = "{% for it in items %} {{ loop.index0 }}{% endfor %}"

prompt = ("system", template, {'mode': 'unsafe'})
items = [1, 2, 3]

message = ChatPromptTemplate.from_messages(
    messages=prompt,
    template_format="jinja2",
).format_messages(items=items)

print(message[0].content)
```

## Conclusion

When using `ChatPromptTemplate` with `template_format="jinja2"`, LangChain restricts Jinja attribute access to prevent template injection and data exfiltration. However, this restriction blocks standard Jinja loop helpers like `loop.index0`. By using a less restricted Jinja environment or an explicitly "unsafe / trusted" mode, we can overcome this limitation and use more complex templates with LangChain.

## Reference
- [Source](https://github.com/langchain-ai/langchain/issues/34052)