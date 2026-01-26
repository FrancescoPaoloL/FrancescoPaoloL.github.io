---
layout: default
title: "A small consideration regarding LLM Prompt Formatting"
date: 2026-01-26
---

## What

Working with instruction-tuned language models like Qwen makes a proper prompt formatting the real difference between coherent responses and "random output".

## The Three-Part Structure

Most modern chat models expect prompts in three distinct parts:

**System**: Defines the model's role and behavior
```
"You are a helpful assistant. Answer questions directly and accurately."
```

**User**: Contains the actual question or request
```
"What is 2+2?"
```

**Assistant**: Signals where the model should generate its response
```
(model generates answer here)
```

## Why This Matters

Because these models saw millions of conversations structured this way, during training. So, when they recognize this format, they already know how to respond "appropriately".

Without it: unpredictable results.

## Practical Impact

In the OWASP LLM Security Demo project, given same model and same question but implementing proper Qwen instruction formatting transformed response quality dramatically.

Without formatting: "What is 2+2? is a question that has been asked throughout history..."

With formatting: "2 + 2 equals 4."

