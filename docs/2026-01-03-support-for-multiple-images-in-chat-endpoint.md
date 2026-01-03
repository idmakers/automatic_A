---
title: "Support for Multiple Images in /chat Endpoint"
tags:
  - multiple-images
  - chat-endpoint
---

# Support for Multiple Images in /chat Endpoint

## Core Problem

The current implementation of the `/chat` endpoint only supports a single image, which introduces complexity when performing RAG with images embedded in base64. This limitation requires manual processing to reconstruct full images and make separate requests for each retrieved image.

## Solution & Analysis

To simplify this process, we can propose an enhancement to support multiple images in a single request. Ollama already supports multiple images, but most models do not.

```console
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

# minicpm-v:8b-2.6-q4_K_M
The first image shows a small white puppy sitting on what appears to be concrete steps. The puppy has bright eyes and is wearing a red collar with a bell attached.

The second image depicts an orange kitten lying down, looking directly at the camera. It has large, expressive greenish-yellow eyes and pointed ears typical of many cat breeds.

# moondream:1.8b-v2-fp16
In the image, there is a cute orange kitten sitting on top of an object that appears to be either a couch or a bed. The kitten seems to be looking directly at the camera and has its eyes open wide, capturing attention from viewers. The scene exudes warmth and playfulness as this adorable feline takes center stage in the composition.

# llava
The image shows a small, kitten-like cat with a white coat and tan-colored ears. It has striking blue eyes and is wearing a red collar with a tag. The cat appears to be sitting or lying down on what looks like a wooden floor or deck.
```

## Conclusion

Supporting multiple images in the `/chat` endpoint would simplify workflows and reduce overhead, particularly in scenarios involving RAG with images embedded in base64. While Ollama already supports multiple images, most models do not, highlighting the need for this enhancement.