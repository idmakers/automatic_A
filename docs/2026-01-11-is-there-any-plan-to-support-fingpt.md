---
title: "Will FinGPT Be Supported by Ollama Soon?"
tags:
  - natural-language-processing
  - machine-learning-models
---

# Will FinGPT Be Supported by Ollama Soon?

## Core Problem

FinGPT, an open-source finite difference GPT-based model for financial applications, has gained popularity in recent times. However, its usage is limited to specific models and datasets due to the lack of official support from the Ollama team.

## Solution & Analysis

To determine if FinGPT will be supported by Ollama soon, we need to analyze the current state of Ollama's development roadmap and community engagement.

```python
import requests

# Check Ollama's GitHub repository for updates on FinGPT support
response = requests.get("https://api.github.com/repos/ollama/ollama/issues")
issues = response.json()

# Filter issues related to FinGPT support
fin_gpt_issues = [issue["title"] for issue in issues if "FinGPT" in issue["title"]]

if fin_gpt_issues:
    print(f"Found {len(fin_gpt_issues)} issues related to FinGPT support.")
else:
    print("No issues found related to FinGPT support.")

# Check Ollama's community engagement on GitHub discussions
discussions = requests.get("https://api.github.com/repos/ollama/ollama/discussions")
discussion_topics = [topic["title"] for topic in discussions.json()]

fin_gpt_discussions = [topic for topic in discussion_topics if "FinGPT" in topic]

if fin_gpt_discussions:
    print(f"Found {len(fin_gpt_discussions)} discussions related to FinGPT support.")
else:
    print("No discussions found related to FinGPT support.")
```

## Conclusion

While there is currently no official plan for Ollama to support FinGPT, the community engagement and issue tracking suggest that developers are actively exploring ways to integrate FinGPT into Ollama. As the community continues to grow and contribute, it's likely that we'll see updates on FinGPT support in the near future.

<!-- ADSENSE_PLACEHOLDER -->

## Reference
- [Source](https://github.com/ollama/ollama/issues/1709)