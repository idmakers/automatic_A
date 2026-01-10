---
title: "Unlocking Multimodal Understanding with InternVL 1.5"
tags:
  - Large Language Models
  - Multimodal Understanding
  - Open-Source Models

---

# Unlocking Multimodal Understanding with InternVL 1.5

## Core Problem

The capability gap between open-source and proprietary commercial models in multimodal understanding has been a significant challenge. Existing large language models (LLMs) lack the ability to effectively process visual information, leading to limitations in tasks such as image captioning, visual question answering, and document summarization.

## Solution & Analysis

InternVL 1.5 addresses this issue by introducing three simple designs that enhance multimodal understanding capabilities:

### Strong Vision Encoder
```python
import torch
import torchvision
from transformers import ViTFeatureExtractor, ViTPretTokenizer

# Load pre-trained InternViT-6B model
model = ViTModel.from_pretrained('internvit/InternViT-6B')

# Define a custom feature extractor and tokenizer
feature_extractor = ViTFeatureExtractor.from_pretrained('internvit/InternViT-6B')
tokenizer = ViTPretTokenizer.from_pretrained('internvit/InternViT-6B')

# Use the strong vision encoder to boost visual understanding capabilities
def strong_vision_encoder(image):
    # Preprocess the image
    inputs = feature_extractor(images=image, return_tensors='pt')
    
    # Pass the preprocessed image through the model
    outputs = model(**inputs)
    
    # Return the output features
    return outputs.last_hidden_state[:, 0, :]
```

### Dynamic High-Resolution
```python
import torch
from PIL import Image

def dynamic_high_resolution(image_path):
    # Load the input image
    image = Image.open(image_path)
    
    # Calculate the aspect ratio and resolution of the image
    width, height = image.size
    aspect_ratio = width / height
    
    # Divide the image into tiles based on the aspect ratio and resolution
    tile_size = 448
    num_tiles_x = int(width / tile_size)
    num_tiles_y = int(height / tile_size)
    
    # Process each tile separately
    for i in range(num_tiles_x):
        for j in range(num_tiles_y):
            # Extract the current tile from the image
            x0 = i * tile_size
            y0 = j * tile_size
            x1 = (i + 1) * tile_size
            y1 = (j + 1) * tile_size
            
            # Preprocess and pass the tile through the model
            inputs = feature_extractor(images=image.crop((x0, y0, x1, y1)), return_tensors='pt')
            outputs = model(**inputs)
            
            # Return the output features for the current tile
            yield {
                'image': image_crop,
                'output': outputs.last_hidden_state[:, 0, :]
            }
```

### High-Quality Bilingual Dataset
```python
import pandas as pd

# Load the high-quality bilingual dataset
df = pd.read_csv('internvl_chat_v1_5_dataset.csv')

# Define a function to create pairs of question-answer examples from the dataset
def create_pairs(df):
    # Create pairs of English and Chinese question-answer examples
    english_questions = df['English Questions']
    chinese_answers = df['Chinese Answers']
    
    # Return the pairs as a list of dictionaries
    return [{'english_question': q, 'chinese_answer': a} for q, a in zip(english_questions, chinese_answers)]
```

## Conclusion

InternVL 1.5 provides a powerful solution for multimodal understanding by introducing three simple designs that enhance visual information processing capabilities. By leveraging the strong vision encoder, dynamic high-resolution feature extraction, and high-quality bilingual dataset, developers can build more robust and effective LLMs for tasks such as image captioning, visual question answering, and document summarization.

<!-- ADSENSE_PLACEHOLDER -->

## Reference
- [Source](https://github.com/ollama/ollama/issues/4257)