---
title: "Supporting InternVL-Chat-V1.5 in Hugging Face Models"
tags:
  - multimodal large language models
  - open-source models
  - proprietary commercial models
---

# Supporting InternVL-Chat-V1.5 in Hugging Face Models

## Core Problem
The gap between open-source and proprietary commercial models in multimodal understanding is a significant challenge in the field of natural language processing (NLP). The need for more capable and transferable models has led to the introduction of InternVL 1.5, an open-source multimodal large language model.

## Solution & Analysis
To support InternVL-Chat-V1.5 in Hugging Face models, you can follow these steps:

### Step 1: Install Required Libraries

```bash
pip install transformers torch torchvision
```

### Step 2: Load the Model

```python
from transformers import LLAMACheckpointReader

model_name = "OpenGVLab/InternVL-Chat-V1.5"

reader = LLAMACheckpointReader(model_name)
```

### Step 3: Fine-Tune the Model (Optional)

To fine-tune the model on your specific dataset, you can use the following code:

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("OpenGVLab/InternVL-Chat-V1.5")
model = AutoModelForCausalLM.from_pretrained("OpenGVLab/InternVL-Chat-V1.5")

# Your dataset and labels here
train_dataset = ...  # Your dataset loader
labels = ...  # Your label values

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model.to(device)

# Fine-tuning the model
optimizer = AdamW(model.parameters(), lr=1e-5)
for epoch in range(5):
    for batch in train_dataset:
        input_ids = tokenizer.encode(batch["input_text"], return_tensors="pt").to(device)
        attention_mask = tokenizer.encode(batch["input_text"], return_tensors="pt", max_length=512, padding="max_length", truncation=True).to(device)
        labels = torch.tensor(batch["labels"]).to(device)
        
        # Zero gradients
        optimizer.zero_grad()
        
        # Forward pass
        outputs = model(input_ids, attention_mask=attention_mask, labels=labels)
        
        # Backward and optimize
        loss = outputs.loss
        loss.backward()
        optimizer.step()

    print(f"Epoch {epoch+1}, Loss: {loss.item():.4f}")
```

## Conclusion
Supporting InternVL-Chat-V1.5 in Hugging Face models can be achieved by following these steps. By leveraging the capabilities of this open-source multimodal large language model, you can bridge the capability gap between open-source and proprietary commercial models in multimodal understanding.

## Reference
- [Source](https://github.com/ollama/ollama/issues/4257)