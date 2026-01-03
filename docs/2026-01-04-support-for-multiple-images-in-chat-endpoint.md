---
title: "Support for Multiple Images in /chat Endpoint"
tags:
  - multiple-images
  - ollama-api
  - chat-endpoint
---

# Support for Multiple Images in /chat Endpoint

## Core Problem
The current implementation of the /chat endpoint only supports a single image, which introduces an additional layer of complexity when performing RAG (Reinforcement Algorithm with Gaze) with images embedded in base64.

## Solution & Analysis
To simplify this process, we can leverage existing libraries and frameworks that support multiple images. In the GitHub repository ollama/ollama, there is a note that ollama supports multiple images, but most models do not.

For example, using the `base64` library in Python, we can pass multiple images in a single request:

```bash
$ for i in minicpm-v:8b-2.6-q4_K_M moondream:1.8b-v2-fp16 llava ; do 
  echo $i ; 
  echo '{"model": "'$i'",
         "messages":[{
            "role":"user","content":"describe the animals shown in the images",
            "images": [
              "'"$(base64 puppy.jpg)"'",
              "'"$(base64 kitten.jpg)"'"
            ]
          }],
         "stream":false}' | curl -s http://localhost:11434/api/chat -d @- | jq -r .message.content ;
done
```

In this example, the `base64` library is used to encode the images and pass them in a single request. The response from the API can then be summarized into one.

Another approach is to use the LLAVA model, which merges two images and describes a scene with multiple objects. This allows for more complex descriptions of scenes with multiple images.

## Conclusion
Supporting multiple images in the /chat endpoint would greatly simplify workflows and reduce overhead in scenarios like RAG with images embedded in base64. While there is currently no plan to add this feature, existing libraries and frameworks can be used as a workaround.